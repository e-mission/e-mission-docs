# Configuring Authentication
---

The end to end system now supports multiple auth methods.
On the server, the auth method is specified in the `webserver.conf` file - e.g. `"auth": "skip"`. Further configuration of the specified auth method is in the `conf/net/auth/<auth_method>.json` file.

```
  "server" : {
    "host" : "localhost",
    "port" : "8080",
    "__comment": "1 hour = 60 min = 60 * 60 sec",
    "timeout" : "3600",
    "auth": "skip"
  },
```

On the client, the auth method is specified in `www/json/connectionConfig.json`. You can find sample files for connecting physical devices to the local server (`www/json/connectionConfig.physical_device2localhost.json.sample`), for connecting to production servers using a variety of authentication methods (e.g. `www/json/connectionConfig.googleauth.json.sample`).

The currently supported auth methods and their configurations are described below.

### `skip`/`prompted-auth` ###

This is the default, and is intended to make the development process easier.  It requires no additional configuration, because it does no verification. It simply assumes that the incoming token is the "email address" and all email addresses are valid.

Anybody who knows the incoming email address/token/identifier can access the user's data.  If this identifier is well known, like an email address or phone number, this option is horribly insecure. If this identifier is secret, like a password or a token from a list, then it is somewhat secure. However, it is a very unsophisticated method - it does not enforce strong passwords, or support password recovery, 2 factor authentication or security questions. If your user loses their password, their password is compromised, or they want to switch their phone, all their data is lost.

The default prompt for prompted-auth is "Dummy dev mode". If you want to use a different prompt, you have to specify it as prompt - e.g. in the `www/json/connectionConfig.json`:

```
    "android": {
        "auth": {
            "method": "prompted-auth",
            "prompt": "Enter a unique passphrase",
            "clientID": "ignored"
        }
    },
    "ios": {
        "auth": {
            "method": "prompted-auth",
            "prompt": "Enter a unique passphrase",
            "clientID": "ignored"
        }
    }
```

Read an related usage issue [here](https://github.com/e-mission/e-mission-docs/issues/577). 

### `token_list`/`prompted-auth` ###

This method is designed for short, controlled studies with extensive participant interaction. A list of pre-created _tokens_ (essentially pre-recreated passwords) is stored on the server, and participants are handed tokens in person as they are recruited. The incoming token is checked against the pre-created list - if it exists, it is valid, if not, it is not.

This is similar to the `skip` method because the user enters the token directly into the app. The only difference is that the `skip` method does not validate the token in any way - users can freely create new tokens, while the `token_list` enforces that the token is from the configured list.

#### Configuration ####

The only parameter is the path to the file with the list of pre-created tokens - e.g. in `conf/net/auth/token_list.json` 

```
{
  "token_list": "conf/net/token_list",
}
```

where `conf/net/token_list` is something like (from https://github.com/redacted/XKCD-password-generator)

```
correct_horse_battery_staple
collar_highly_asset_ovoid_sultan
caper_hangup_addle_oboist_scroll
couple_honcho_abbot_obtain_simple
```

#### Generating a token list ####
Generating a large, valid token list is important to ensure security. For users who are not comfortable with scripting, random.org's password generator is a good option. To use it:

1. Go to https://www.random.org/passwords/
1. Switch to advanced mode
1. *IMPORTANT* Select "bare-bones text document" output
1. Copy and paste the generated passwords into a text editor.
   - Built-in text editors are: Notepad on windows and vi/vim on \*ix and OSX
   - You can also download text editors such as vim (https://vim.sourceforge.io/download.php#pc), Sublime Text (http://www.sublimetext.com/3) and Atom (https://atom.io/)
   - Do *NOT* use document editors such as microsoft word - they might add additional formatting that will mess up the token matching.
1. Save as plain text

The token file should be readable with the following script.

```
$ python
In [1]: raw_token_list = open("conf/net/token_list").readlines()

In [2]: token_list = [t.strip() for t in raw_token_list]

In [3]: print token_list
```
