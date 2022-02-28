# Install Supervisor on Windows

Supervisor is a client/server system that allows its users to control a number of processes on Windows operating system.


## Install Cygwin
Before you start, you need to install [cygwin](https://www.cygwin.com/install.html), download the installation file and run it, during the installation, in the select package step, change the view to Category

 - Select Interpreters then check **Python 3** to install it.
 - Select Admin then check **cygrunsrv** to install it.
 

## Install pip
 - Download [get-pip.py](https://bootstrap.pypa.io/get-pip.py) to `<CYGWIN_INSTALL_PATH>/home/<USER_DIRECTORY>`
 - Open Cygwin Terminal and run the following command: `python get-pip.py`



## Install Supervisor
Open Cygwin Terminal, and run the following command: `pip install supervisor`


## Check Supervisor installation
After installing supervisor, you can check the installation by running the command: `echo_supervisord_conf`


## Export Supervisor Configuration
If the installation succeed, you can export the supervisor configuration by running this command: `echo_supervisord_conf > /etc/supervisord.conf`

The configuration file will be exported in `<CYGWIN_INSTALL_PATH>/etc` directory

## Edit Supervisor configuration
Open supervisord.conf with your text editor then :

Search and comment **[unix_http_server]** block by adding semicolon at the start of each line.

    ;[unix_http_server]
    ;file=/tmp/supervisor.sock
    ;chmod=0700
    ;chown=nobody:nogroup
    ;username=user
    ;password=1234


Search and uncomment **[inet_http_server]** block by removing semicolon from the start of each line

    [inet_http_server]          ; inet (TCP) server disabled by default
    port=127.0.0.1:9001         ; ip_address:port specifier, *:port for all iface
    username=user               ; default is no username (open server)
    password=1234               ; default is no password (open server)

Search and edit **[supervisorctl]** as the following

    [supervisorctl]
    ;serverurl=unix:///tmp/supervisor.sock  ; use a unix:// URL  for a unix socket
    serverurl=http://127.0.0.1:9001         ; use an http:// url to specify an inet socket
    username=user                           ; should be same as in [*_http_server] if set
    password=1234                           ; should be same as in [*_http_server] if set
    ;prompt=mysupervisor                    ; cmd line prompt (default "supervisor")
    ;history_file=~/.sc_history             ; use readline history if available

Edit **[include]** as the following

    [include]
    files = /etc/supervisor.d/*.conf

Save and close the configuration file

Create the `supervisor.d` folder in `<CYGWIN_INSTALL_PATH>/etc/`

Close the Terminal

## Install supervisor service
Open the terminal as administrator and run the following command

    cygrunsrv --install Supervisor --path <PATH_TO_PYTHON_BINARY> --args "<PATH_TO_SUPERVISOR_SCRIPT> -n -c <PATH_TO_SUPERVISOR_CONFIG>"

- **<PATH_TO_PYTHON_BINARY>:** Python binary that you can find in **/bin**
- **<PATH_TO_SUPERVISOR_SCRIPT>:** Supervisor script that you can find in **/usr/local/bin**
- **<PATH_TO_SUPERVISOR_CONFIG>:** Supervisor configuration file that you can find in **/etc**

### Example:

    cygrunsrv --install Supervisor --path /bin/python --args "/usr/local/bin/supervisord -n -c /etc/supervisord.conf"

## Program configurations
Put your program and group configurations in `<CYGWIN_INSTALL_PATH>/etc/supervisor.d` directory, here is an example:

    [program:laravel-worker]
    process_name=%(program_name)s
    command=C:/Wamp/bin/php/php7.4.16/php.exe C:/Wamp/www/laravel/artisan queue:work
    autostart=true
    autorestart=true
    stopasgroup=true
    killasgroup=true
    redirect_stderr=true

