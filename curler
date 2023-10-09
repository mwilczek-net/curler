#!/bin/zsh

# required for zparseopts
zmodload zsh/zutil

CURRENT_PATH="$(cd "$(dirname "$0")" && pwd -P)"

PATH_CONFIGS="configs"
PATH_HEADERS="headers"
PATH_DATA="data"

alias python=python3.11

url=""

config_file=""
headers_file=""
data_file=""

format_json=""

list_configs=""
list_headers=""
list_data=""

init_environment=""
print_help=""

function parse_parameters {
    zparseopts -D -E -F - \
            u:=url -url:=url \
            K:=config_file -configs:=config_file \
            H:=headers_file -headers:=headers_file \
            D:=data_file -data:=data_file \
            -json=format_json \
            -listConfigs=list_configs \
            -listHeaders=list_headers \
            -listData=list_data \
            -init=init_environment \
            h=print_help -help=print_help \
        || { print_help_content; exit 2 }

        url="${url/(-u =|--url =|-u |--url )/}"
        config_file="${config_file/(-K =|--configs =|-K |--configs )/}"
        headers_file="${headers_file/(-H =|--headers =|-H |--headers )/}"
        data_file="${data_file/(-D =|--data =|-D |--data )/}"
}

function print_help_content {
cat <<EOF--
  curler is a curl wrapper that shift focus to reusable files rather than building long commands.

  --init
        Initialise current directory with required paths.
        Generates examples

  -u URL | --url URL
        Optional URL to endpoint.

  -K FILE | --configs FILE
        Path to configs FILE form '${PATH_CONFIGS}' directory. Wihtout '${PATH_CONFIGS}' prefix.
        If config file is in '${PATH_CONFIGS}/my_config.cfg' then use: 'curler -K my_config.cfg'
        If file in nested in '${PATH_CONFIGS}/xyz/another.cfg' then use: 'curler -K xyz/another.cfg'

  -H FILE | --headers FILE
        Path to hdeaders FILE form '${PATH_HEADERS}' directory. Wihtout '${PATH_HEADERS}' prefix.
        If headers file is in '${PATH_HEADERS}/my_headers.txt' then use: 'curler -K my_headers.txt'
        If file in nested in '${PATH_HEADERS}/xyz/headers.txt' then use: 'curler -K xyz/headers.txt'

  -D FILE | --data FILE
        Path to data FILE form '${PATH_DATA}' directory. Wihtout 'data' prefix.
        If data file is in '${PATH_DATA}/my_data.json' then use: 'curler -K my_data.json'
        If file in nested in '${PATH_DATA}/xyz/data.xml' then use: 'curler -K xyz/data.xml'

  --json
        Format output as JSON string.
        Uses Python - invalid stirng may print only error

  --listConfigs
        List all config files form ${PATH_CONFIGS}

  --listHeaders
        List all headers files form ${PATH_HEADERS}

  --listData
        List all headers files form ${PATH_DATA}

  -h | --help
        Print this help
EOF--
}

function execute_environment_initialization {
    if [ -z "${init_environment}" ]; then
        return
    fi

    if [ -d "${PATH_CONFIGS}" ] && [ -d "${PATH_HEADERS}" ] && [ -d "${PATH_DATA}" ]; then
        echo "Already initialised! Directories exists: '${PATH_CONFIGS}', '${PATH_HEADERS}', '${PATH_DATA}'"
        exit 2
    fi

    # basic directories
    mkdir -p "${PATH_CONFIGS}"
    mkdir -p "${PATH_HEADERS}"
    mkdir -p "${PATH_DATA}"

    path_configs_demo="${PATH_CONFIGS}/demo"
    path_headers_demo="${PATH_HEADERS}/demo"
    path_data_demo="${PATH_DATA}/demo"

    # demo
    mkdir -p "${path_configs_demo}"
    mkdir -p "${path_headers_demo}"
    mkdir -p "${path_data_demo}"

cat <<EOF-- > "${path_configs_demo}/lorem_pixum_list.cfg"
# config files for curl accepts comments
url = "https://picsum.photos/v2/list"
request = "GET"

EOF--

cat <<EOF-- > "${path_configs_demo}/lorem_pixum_random_details.cfg"
# config files for curl accepts comments
url = "https://picsum.photos/seed/picsum/info"
request = "GET"

EOF--

cat <<EOF-- > "${path_configs_demo}/yoda.cfg"
# config files for curl accepts comments
url = "https://api.funtranslations.com/translate/yoda.json"
request = "POST"

EOF--

cat <<EOF-- > "${path_configs_demo}/country_details.cfg"
# config files for curl accepts comments
url = "https://countries.trevorblades.com/graphql"
request = "POST"

EOF--

cat <<EOF-- > "${path_headers_demo}/lorem_pixum.txt"
Content-Type: application/json
Accept: application/json
EOF--

cat <<EOF-- > "${path_headers_demo}/yoda.txt"
Accept: application/json
X-Funtranslations-Api-Secret: <api_key>

EOF--

cat <<EOF-- > "${path_headers_demo}/country_details.txt"
Content-Type: application/json
Accept: application/json

EOF--

cat <<EOF-- > "${path_data_demo}/yoda.txt"
text="Master Obiwan has lost a planet while learning curl."

EOF--

cat <<EOF-- > "${path_data_demo}/country_details_pl.graphql"
{
    "query": "query country(\$code: ID!) {
        country(code: \$code) {
            name
            native
            capital
            emoji
            currency
            languages {
            code
            name
            }
        }
    }
    ",
    "variables": {
        "code": "PL"
    }
}

EOF--


cat <<EOF-- > "curler_examples.sh"
#!/bin/zsh

./curler.sh -K 'demo/lorem_pixum_list.cfg' -H 'demo/lorem_pixum.txt'
./curler.sh -K 'demo/lorem_pixum_list.cfg' -H 'demo/lorem_pixum.txt' --json

./curler.sh -K 'demo/lorem_pixum_random_details.cfg' -H 'demo/lorem_pixum.txt'
./curler.sh -K 'demo/lorem_pixum_random_details.cfg' -H 'demo/lorem_pixum.txt' --json

./curler.sh -K 'demo/yoda.cfg' -H 'demo/yoda.txt' -D 'demo/yoda.txt'
./curler.sh -K 'demo/yoda.cfg' -H 'demo/yoda.txt' -D 'demo/yoda.txt' --json

./curler.sh -K 'demo/country_details.cfg' -H 'demo/country_details.txt' -D 'demo/country_details_pl.graphql'
./curler.sh -K 'demo/country_details.cfg' -H 'demo/country_details.txt' -D 'demo/country_details_pl.graphql' --json
EOF--

    echo "Initialised"
    exit 0
}

function print_help_content_if_requested {
    [ -n "${print_help}" ] && { print_help_content ; exit 1 }
}

function list_current_directory_recursively {
    echo " --- ${1} --- "
    find . -type f | cut -c 3-
}

function list_files {
    [ -n "${list_configs}" ] && {
        pushd $PATH_CONFIGS
        list_current_directory_recursively "CONFIGS"
        popd
    }

    [ -n "${list_headers}" ] && {
        pushd $PATH_HEADERS
        list_current_directory_recursively "HEADERS"
        popd
    }

    [ -n "${list_data}" ] && {
        pushd $PATH_DATA
        list_current_directory_recursively "DATA"
        popd
    }

    [ -n "${list_configs}" ] || [ -n "${list_headers}" ] || [ -n "${list_data}" ] && exit 0
}

function run_curl {
    curl_command="curl"
    [ -n "${config_file}" ] && curl_command="${curl_command} --config '${PATH_CONFIGS}/${config_file}'"
    [ -n "${headers_file}" ] && curl_command="${curl_command} -H '@${PATH_HEADERS}/${headers_file}'"
    [ -n "${data_file}" ] && curl_command="${curl_command} -d '@${PATH_DATA}/${data_file}'"
    [ -n "${url}" ] && curl_command="${curl_command} ${url}"

    >&2 echo "----------------------------------------------------------------------"
    >&2 echo "$curl_command"
    >&2 echo "----------------------------------------------------------------------"

    # space because
    eval " $curl_command"
}

function run_curl_wrapper {
    if [ -z "${config_file}" ] && [ -z "${headers_file}" ] && [ -z "${data_file}" ] && [ -z "${url}" ]; then
        return
    fi

    if [ -n "${format_json}" ]; then
        run_curl | python -m json.tool
        exit_codes=("${pipestatus[@]}")
    else
        run_curl
        exit_codes=("${pipestatus[@]}")
    fi

    for code in $exit_codes
    do
        [ "${code}" -ne "0" ] && exit "${code}"
    done

    exit 0
}

parse_parameters $*

print_help_content_if_requested
execute_environment_initialization

list_files
run_curl_wrapper

# If nothing selected
print_help_content

