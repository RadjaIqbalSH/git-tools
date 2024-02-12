#!/bin/bash
# Femaledaily Custom Git Tools

source_baranch=""

TrimString() {
  str=$(echo "$1" | tr '_ ' '--')
  echo "$str"
}

UppercaseString () {
  str=$(echo "$1" | tr '[:lower:]' '[:upper:]')
  echo "$str"
}

LowercaseString () {
  str=$(echo "$1" | tr '[:upper:]' '[:lower:]')
  echo "$str"
}

GetListSprintBranch () {
  branch_list=$(git for-each-ref --sort=-committerdate refs/heads/sprint/ refs/remotes/origin/sprint/ --format='%(refname)')
  clean_branch_list=$(echo "$branch_list" | sed 's/^refs\/heads\///; s/^refs\/remotes\/origin\///' | awk '!seen[$1]++')
  remove_duplicate_branch_list=$(echo -e "$clean_branch_list")
  echo "$remove_duplicate_branch_list"
}

SelectBranch () {
  payload="$1"
  IFS=$'\n' read -d '' -r -a options <<< "$(printf '%s\0' "$payload")"
  selected=0
  while true; do
    clear
    echo "Select an option (use the up/down arrow keys, press Enter to confirm):"
    for i in "${!options[@]}"; do
      if [ $i -eq $selected ]; then
        echo -e "\e[1;36m➤ ${options[$i]}\e[0m"
      else
        echo "  ${options[$i]}"
      fi
    done

    read -r -s -n 1 key
    case $key in
      A) # Arrow Up key
        ((selected > 0)) && ((selected--))
        ;;
      B) # Arrow Down key
        ((selected < ${#options[@]} - 1)) && ((selected++))
        ;;
      "") # Enter key
        clear
        source_baranch="${options[$selected]}"
        break
        ;;
    esac
  done

}

# Create new srprint branch
CreateSprintBranch () {
  read -p "Sprint name : " -r sprint_name
  branch_name="sprint/$sprint_name"
  lowercase_branch_name=$(LowercaseString "$branch_name")
  clean_branch_name=$(TrimString "$lowercase_branch_name")
  git fetch --all
  git checkout -b "$clean_branch_name" origin/master
  git push origin "$clean_branch_name"
  exit 1
}

CreateHotBranch () {
  while true; do
    read -p "Ticket code : " -r ticket_code
    if [ -z "$ticket_code" ]; then
      echo ""
    else
      break
    fi
  done
  while true; do
    read -p "Bug name : " -r bug_name
    if [ ${#bug_name} -gt 20 ]; then
      echo "Nama bug melebihi 20 karakter, silakan coba lagi."
    else
      break
    fi
  done
  uppercase_ticket_code=$(UppercaseString "$ticket_code")
  lower_case_bug_name=$(LowercaseString "$bug_name")
  branch_name="hotfix/${uppercase_ticket_code:-FREE}-$lower_case_bug_name"
  clean_branch_name=$(TrimString "$branch_name")
  git checkout -b "$clean_branch_name" origin/master
  exit 1
}

createFeatureBranch () {
  if [ -z "$1" ]; then
    options=$(GetListSprintBranch)
    if [ -z "$options" ]; then
      read -p "Sprint branch name : " -r sprint_branch_name
      source_baranch="$sprint_branch_name"
    else
      SelectBranch "$options"
    fi
  else
    source_baranch="$1"
  fi

  while true; do
    read -p "Ticket code : " -r ticket_code
    if [ -z "$ticket_code" ]; then
      echo "Ticket code belum di isi, silakan coba lagi."
    else
      break
    fi
  done

  while true; do
    read -p "Feature name : " -r feature_name
    if [ ${#feature_name} -gt 20 ]; then
      echo "Nama bug melebihi 20 karakter, silakan coba lagi."
    else
      break
    fi
  done

  uppercase_ticket_code=$(UppercaseString "$ticket_code")
  lower_case_feature_name=$(LowercaseString "$feature_name")
  branch_name="feature/$uppercase_ticket_code-$lower_case_feature_name"
  clean_branch_name=$(TrimString "$branch_name")

  git checkout -b "$clean_branch_name" origin/"$source_baranch"

  exit 1
}

createFixBranch () {
  if [ -z "$1" ]; then
    options=$(GetListSprintBranch)
    if [ -z "$options" ]; then
      read -p "Sprint branch name : " -r sprint_branch_name
      source_baranch="$sprint_branch_name"
    else
      SelectBranch "$options"
    fi
  else
    source_baranch="$1"
  fi

  while true; do
    read -p "Ticket code : " -r ticket_code
    if [ -z "$ticket_code" ]; then
      echo "Ticket code belum di isi, silakan coba lagi."
    else
      break
    fi
  done

  while true; do
    read -p "Bug name : " -r bug_name
    if [ ${#bug_name} -gt 20 ]; then
      echo "Nama bug melebihi 20 karakter, silakan coba lagi."
    else
      break
    fi
  done

  uppercase_ticket_code=$(UppercaseString "$ticket_code")
  lower_case_bug_name=$(LowercaseString "$bug_name")
  branch_name="fix/$uppercase_ticket_code-$lower_case_bug_name"
  clean_branch_name=$(TrimString "$branch_name")

  git checkout -b "$clean_branch_name" origin/"$source_baranch"

  exit 1
}

Commit () {
  type_select=0
  type_selected=""
  type_options=("feat" "fix" "chore" "docs" "style" "refactor" "perf" "test" "build" "ci" "revert")
  while true; do
    clear
    echo "Select an option (use the up/down arrow keys, press Enter to confirm):"
    for i in "${!type_options[@]}"; do
      if [ $i -eq $type_select ]; then
        echo -e "\e[1;36m➤ ${type_options[$i]}\e[0m"
      else
        echo "  ${type_options[$i]}"
      fi
    done

    read -r -s -n 1 key
    case $key in
      A) # Arrow Up key
        ((type_select > 0)) && ((type_select--))
        ;;
      B) # Arrow Down key
        ((type_select < ${#type_options[@]} - 1)) && ((type_select++))
        ;;
      "") # Enter key
        clear
        type_selected="${type_options[$type_select]}"
        break
        ;;
    esac
  done
  while true; do
    read -p "Description : " -r description
    if [ ${#description} -gt 50 ]; then
      echo "Nama bug melebihi 50 karakter, silakan coba lagi."
    else
      break
    fi
  done
  git commit -m "$type_selected: $description"
  exit 1
}

Push () {
  current_branch=$(git rev-parse --abbrev-ref HEAD)
  git push origin "$current_branch"
  exit 1
}

while true; do
  case $1 in
    sprint)
      CreateSprintBranch
      ;;
    hotfix)
      CreateHotBranch
      ;;
    feature)
      createFeatureBranch "$2"
    ;;
    fix)
      createFixBranch "$2"
    ;;
    commit)
      Commit
    ;;
    push)
      Push
    ;;
    --help|-h)
      echo "Femaledaily Git Tools"
      echo "Usage: fdg [OPTIONS]"
      echo "Options:"
      echo "  sprint                  Create, checkout and push to remote a new sprint branch from origin master"
      echo "  hotfix                  Create a new hotfix branch from origin master"
      echo "  feature                 Create a new feature branch from selected sprint branch"
      echo "  feature [source_branch] Create a new feature branch from source branch arg"
      echo "  fix                     Create a new bugfix branch from selected sprint branch"
      echo "  fix [source_branch]     Create a new bugfix branch from source branch arg"
      echo "  commit                  Create a new commit"
      echo "  push                    Push branch"
      echo "  -h, --help              Display this help message"
      echo "  -v, --version           Display tools version"
      exit 1
    ;;
    --version|-v)
      echo "1.0.1"
      exit 1
    ;;
    *)
      echo "Welcome to Femaledaily Git Tools v1.0.1"
      exit 1
      ;;
  esac
done
