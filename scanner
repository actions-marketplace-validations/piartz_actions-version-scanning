#!/bin/bash
bold=$(tput bold)
normal=$(tput sgr0)
DEFAULT_CONFIG_FILE=./CONFIG
CONFIG_FILE=$DEFAULT_CONFIG_FILE
YAML_PATH=./scan/

function help () {
    echo "AVS - Actions Version Scanning"
    echo
    echo "Usage: "
    echo "scanner [-h] [-p]"
    echo
    echo "-h: Help. Open this menu."
    echo "-p: Scan path (can be a directory or file). Overrides the default YAML_PATH var"
}

# Argument parsing
while getopts 'hp:' o;
do
    case "$o" in
    h)
        help
        exit 0
    ;;
    p)
        YAML_PATH=$OPTARG
        if [ ! -e "${YAML_PATH}" ];
        then
            >&2 echo "Argument ${YAML_PATH}: No such file or directory."
            exit 1
        fi
    ;;
    ?)
        >&2 echo "Argument ${o} not found."
        help
        exit 1
    ;;
    esac
done
shift $((OPTIND-1))

if [ $# -gt 0 ];
then
    >&2 echo "Argument ${1} not recognized."
fi

source $CONFIG_FILE

echo
echo "${bold}Action versions not referencing a commit hash${normal}"
echo "-------------"
echo 

# Looking for strings after an '@' outside of the default commit length (40 chars)
grep -Enr '\buses\b:[ \t]+.+\/.+@\b.{1,39}\b' "${YAML_PATH}"
grep -Enr '\buses\b:[ \t]+.+\/.+@\b.{41,255}\b' "${YAML_PATH}"

# For the 40 chars use case:
# Commit hashes only use lowcase non-numeric chars from a-f
# Piping the 40 char result to exclude hash compliant occurences
grep -Enr '\buses\b:[ \t]+.+\/.+@\b.{40}\b' "${YAML_PATH}" | grep -Ev '\buses\b:[ \t]+.+\/.+@\b[a-f0-9]{40}\b'


echo
echo
echo "${bold}Action versions referencing a commit hash${normal}"
echo "-------------"
echo
grep -Enr '\buses\b:[ \t]+.+\/.+@\b.{40}\b' "${YAML_PATH}" | grep -E '\buses\b:[ \t]+.+\/.+@\b[a-f0-9]{40}\b'

# Uncommon edge case: 
# a branch name with 40 chars where alphanumeric characters dont include [g-zG-Z] characters.

