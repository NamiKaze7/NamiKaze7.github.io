[TOC]

## 清理显存

```shell
fuser -v /dev/nvidia*

ps -aux |grep 具体的PID

fuser -v /dev/nvidia0 然后从后往前杀
```

## 查看文件大小

```sh
du -h --max-depth=1
```

Conda:https://zhuanlan.zhihu.com/p/87344422

## shell脚本读取yaml参数

yaml.sh文件

```sh
#!/usr/bin/env bash
# shellcheck disable=SC1003

# Based on https://gist.github.com/pkuczynski/8665367

parse_yaml() {
    local yaml_file=$1
    local prefix=$2
    local s
    local w
    local fs

    s='[[:space:]]*'
    w='[a-zA-Z0-9_.-]*'
    fs="$(echo @ | tr @ '\034')"

    (
        sed -e '/- [^\“]'"[^\']"'.*: /s|\([ ]*\)- \([[:space:]]*\)|\1-\'$'\n''  \1\2|g' |
            sed -ne '/^--/s|--||g; s|\"|\\\"|g; s/[[:space:]]*$//g;' \
                -e 's/\$/\\\$/g' \
                -e "/#.*[\"\']/!s| #.*||g; /^#/s|#.*||g;" \
                -e "s|^\($s\)\($w\)$s:$s\"\(.*\)\"$s\$|\1$fs\2$fs\3|p" \
                -e "s|^\($s\)\($w\)${s}[:-]$s\(.*\)$s\$|\1$fs\2$fs\3|p" |
            awk -F"$fs" '{
            indent = length($1)/2;
            if (length($2) == 0) { conj[indent]="+";} else {conj[indent]="";}
            vname[indent] = $2;
            for (i in vname) {if (i > indent) {delete vname[i]}}
                if (length($3) > 0) {
                    vn=""; for (i=0; i<indent; i++) {vn=(vn)(vname[i])("_")}
                    printf("%s%s%s%s=(\"%s\")\n", "'"$prefix"'",vn, $2, conj[indent-1], $3);
                }
            }' |
            sed -e 's/_=/+=/g' |
            awk 'BEGIN {
                FS="=";
                OFS="="
            }
            /(-|\.).*=/ {
                gsub("-|\\.", "_", $1)
            }
            { print }'
    ) <"$yaml_file"
}

unset_variables() {
    # Pulls out the variable names and unsets them.
    #shellcheck disable=SC2048,SC2206 #Permit variables without quotes
    local variable_string=($*)
    unset variables
    variables=()
    for variable in "${variable_string[@]}"; do
        tmpvar=$(echo "$variable" | grep '=' | sed 's/=.*//' | sed 's/+.*//')
        variables+=("$tmpvar")
    done
    for variable in "${variables[@]}"; do
        if [ -n "$variable" ]; then
            unset "$variable"
        fi
    done
}

create_variables() {
    local yaml_file="$1"
    local prefix="$2"
    local yaml_string
    yaml_string="$(parse_yaml "$yaml_file" "$prefix")"
    unset_variables "${yaml_string}"
    eval "${yaml_string}"
}

# Execute parse_yaml() direct from command line

if [ "x" != "x${1}" ] && [ "x--debug" != "x${1}" ]; then
    parse_yaml "${1}" "${2}"
fi
```

使用文件

```sh
#!/bin/bash

# 定义YAML文件路径
yaml_name="config.yaml"
echo $yaml_name
# 读取YAML文件
set -e
cd "$(dirname "${BASH_SOURCE[0]}")"
source ./yaml.sh

# Debug
DEBUG="$1"

function is_debug() {
    [ "$DEBUG" = "--debug" ] && return 0 || return 1
}

if is_debug; then
    parse_yaml $yaml_name && echo
fi

# Execute
create_variables $yaml_name
```

