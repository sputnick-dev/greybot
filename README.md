# greybot as command-line

This is designed to send advices to internet users who ask questions on the #bash topic, like #bash@freenode back in the days, now #bash@libera, but most for the web using this fork.

Some commands are already modified in markdown, some not. Feel free to contribute if you are interested.

# greybot main script

Edit/replace `~/sources/greybot` to the path you have the clone of 'greybot':

    #!/bin/bash

    # put your own path of the repository
    cd ~/sources/greybot

    # -------8<------------------
    # git clone https://github.com/im14/greybot.git

    shopt -s nullglob

    if [[ $1 == --grep ]]; then
        grep=true
        shift
    fi

    pp() {
        echo "title: $1"
        cat "$1"
        echo '-------8<------------------'
    }

    redirect() {
        if grep 2>/dev/null '^#redirect' "$1"; then
            out="$(< "$1")"
            [[ $out =~ ^#redirect[[:space:]]+(.*) ]]
            file="${BASH_REMATCH[1]}"
        else
            file="$1"
        fi
    }

    if [[ ! $1 ]]; then
        read -p 'Run a full text search, type a pattern >>> '
        ack -i "$REPLY"
    elif [[ -s $1 && $grep != true ]]; then
        redirect "$1"
        pp "$file"
    else
        for file in $1 *$1*; do
            redirect "$file"
            if [[ -s $file ]]; then
                pp "$file"
            fi
        done
    fi
