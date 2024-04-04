```bash
#!/bin/bash

##########################################################
# 备份脚本
#
# 该脚本用于定期备份指定主机上的指定目录到指定位置，并保留指定天数的备份数据。
# 备份频率为每天执行一次，首次执行会拷贝前一天备份数据到当天目录，并保证同步数据和源数据一致。
# 后续每次执行都进行增量备份，不会同步删除源目录删除的文件。
# 备份完成后会记录日志，并删除超过指定天数的旧备份数据。
#
# 需要配置的参数包括：
# - backup_root: 备份根目录
# - days_to_keep: 保留备份天数
# - backup_directories: 备份信息数组，每个元素包含备份Key、源主机、源目录、SSH参数、排除文件
#
# Author: [Your Name]
# Date: [Date]
##########################################################

# 配置
backup_root="/share/homes/xpcheng/backup_remote"  # 备份根目录
log_file="${backup_root}/backup.log" # 日志文件路径

# 备份信息数组：备份Key、源主机、源目录、SSH参数、排除文件
backup_directories=(
    "alinode1|xxx.tech|/opt/program/|ssh -l root -p 22|exclude_alinode1.txt"  # alinode1备份信息
    "debian|xxx.tech|/home/xpcheng/docker-data/|ssh -l root -p 33022|exclude_debian.txt"  # debian备份信息
)
days_to_keep=30  # 保留备份天数

# 日志记录函数
log() {
    echo "$(date +'%Y-%m-%d %H:%M:%S') $1" >> "$2"
}

# 本次同步前清空日志
echo "BACKUP" > "$log_file"

# 执行备份和删除超过指定天数的备份
for backup_info in "${backup_directories[@]}"; do
    IFS='|' read -r -a backup <<< "$backup_info"
    backup_key="${backup[0]}"
    source_host="${backup[1]}"
    source_dir="${backup[2]}"
    ssh_param="${backup[3]}"

    exclude_file="${backup_root}/${backup[4]}"
    backup_dir="${backup_root}/${backup_key}/${backup_key}_$(date +'%Y%m%d')"
    prev_backup_dir="${backup_root}/${backup_key}/${backup_key}_$(date -d "yesterday" +'%Y%m%d')"

    log "===== ${backup_key} =====" "$log_file"
    # 执行备份
    log "Start rsync task" "$log_file"
    # 如果当天备份目录不存在，则递归创建本次备份目录并做同步
    if [ ! -d "${backup_dir}" ] ; then
        log "Creating backup dir" "$log_file"
        mkdir -p "${backup_dir}"

        # 当天第一次同步使用 --delete-excluded 选项
        rsync -e "$ssh_param" \
            "${source_host}:${source_dir}" "${backup_dir}/" \
            --exclude-from "$exclude_file" \
            --link-dest "$prev_backup_dir/" \
            -av \
            --delete-excluded >> "$log_file" 2>&1
    else
    # 当天后续增量同步不使用 --delete-excluded 选项
        log "Existing backup dir" "$log_file"
        # 当天非第一次同步不使用 --delete-excluded 选项
        rsync -e "$ssh_param" \
            "${source_host}:${source_dir}" "${backup_dir}/" \
            --exclude-from "$exclude_file" \
            --link-dest "$prev_backup_dir/" \
            -av >> "$log_file" 2>&1
    fi
    log "End rsync task" "$log_file"

    # 判断执行结果
    if [ $? -eq 0 ]; then
        log "Backup completed successfully" "$log_file"
    else
        log "Backup failed" "$log_file"
        exit 1
    fi

    # 删除过期日志目录
    log "Delete expired backup dir" "$log_file"
    find "${backup_root}/${backup_key}" -maxdepth 1 -type d -name "${backup_key}_*" -mtime "+${days_to_keep}" -exec rm -rf {} \;
    log "Delete successful" "$log_file"
    log "END BACKUP ${backup_key}" "$log_file"
done
log "===== ALL DONE =====" "$log_file"

```