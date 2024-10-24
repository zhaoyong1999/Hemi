#!/bin/bash

# 脚本保存路径
SCRIPT_PATH="$HOME/Hemi.sh"


# 运行节点函数
run_node() {
    DIRECTORY="$HOME/heminetwork_v0.5.0_linux_amd64"

    echo "进入目录 $DIRECTORY..."
    cd "$DIRECTORY" || { echo "目录 $DIRECTORY 不存在。"; exit 1; }

    # 设置 popm-address.json 的权限为可读写
    if [ -f "$HOME/popm-address.json" ]; then
        echo "为 popm-address.json 文件设置权限..."
        chmod 600 "$HOME/popm-address.json"  # 仅当前用户可读写
    else
        echo "$HOME/popm-address.json 文件不存在。"
        exit 1
    fi

    # 显示文件内容
    cat "$HOME/popm-address.json"

    # 导入 private_key
    POPM_BTC_PRIVKEY=$(jq -r '.private_key' "$HOME/popm-address.json")
#    read -p "检查 https://mempool.space/zh/testnet 上的 sats/vB 值并输入 / Check the sats/vB value on https://mempool.space/zh/testnet and input: " POPM_STATIC_FEE
    POPM_STATIC_FEE=350
    export POPM_BTC_PRIVKEY=$POPM_BTC_PRIVKEY
    export POPM_STATIC_FEE=$POPM_STATIC_FEE
    export POPM_BFG_URL="wss://testnet.rpc.hemi.network/v1/ws/public"

    echo "启动节点..."
    pm2 start ./popmd --name popmd
    pm2 save

    echo "按任意键返回主菜单栏..."
}

# 升级版本函数
upgrade_version() {
    URL="https://github.com/hemilabs/heminetwork/releases/download/v0.5.0/heminetwork_v0.5.0_linux_amd64.tar.gz"
    FILENAME="heminetwork_v0.5.0_linux_amd64.tar.gz"
    DIRECTORY="/root/heminetwork_v0.4.4_linux_amd64"
    ADDRESS_FILE="$HOME/popm-address.json"
    BACKUP_FILE="$HOME/popm-address.json.bak"

    echo "备份 address.json 文件..."
    if [ -f "$ADDRESS_FILE" ]; then
        cp "$ADDRESS_FILE" "$BACKUP_FILE"
        echo "备份完成：$BACKUP_FILE"
    else
        echo "未找到 address.json 文件，无法备份。"
    fi

    echo "正在下载新版本 $FILENAME..."
    wget -q "$URL" -O "$FILENAME"

    if [ $? -eq 0 ]; then
        echo "下载完成。"
    else
        echo "下载失败。"
        exit 1
    fi

    echo "删除旧版本目录..."
    rm -rf "$DIRECTORY"

    echo "正在解压新版本..."
    tar -xzf "$FILENAME" -C /root

    if [ $? -eq 0 ]; then
        echo "解压完成。"
    else
        echo "解压失败。"
        exit 1
    fi

    echo "删除压缩文件..."
    rm -rf "$FILENAME"

    # 恢复 address.json 文件
    if [ -f "$BACKUP_FILE" ]; then
        cp "$BACKUP_FILE" "$ADDRESS_FILE"
        echo "恢复 address.json 文件：$ADDRESS_FILE"
    else
        echo "备份文件不存在，无法恢复。"
    fi
    pm2 restart popmd
    echo "版本升级完成！"
    echo "按任意键返回主菜单栏..."
}

# 查看日志函数
view_logs() {
    DIRECTORY="heminetwork_v0.5.0_linux_amd64"

    echo "进入目录 $DIRECTORY..."
    cd "$HOME/$DIRECTORY" || { echo "目录 $DIRECTORY 不存在。"; exit 1; }

    echo "查看 pm2 日志..."
    pm2 logs popmd

    echo "按任意键返回主菜单栏..."
}
