## spip

This is a script/wrapper for populating `~/.pip/pip.conf` with passwords from
the system keychain, running pip with the credentials, and deleting the
passwords from disk when done.  It's intended for Python shops running their
own PyPi proxy with password authentication, but you don't want to leave plain
text passwords on disk in pip.conf.

## Setup

First install a few depenencies.

    pip install requirements.txt

On OSX, open the `Keychain Access` application.  You'll probably need to
unlock the keychain and type in your password a few times.  Double-click the
`System` icon in the `Keychains` menu on the left.  Next, under the File menu,
select `New Password Item`.  Use these settings:

    Keychain Item Name: pip
    Account Name: your pypi proxy username
    Password: your pypi proxy password

Click `Add`.  Next find the newly created Keychain entry and double click on
it.  Click the `Access Control` tab.  Under
`Always allow access by these applications`, click the `+` sign and add
`/usr/bin/security`.  This step is optional and you could also provide the path
to your Python interpreter. This provides sudo-like functionality.  When
running `spip`, you'll need to unlock your Keychain at least once during your
session and you'll have the option to keep it unlocked or not.  If you choose
this option, exit out of all windows, saving changes as you go.

You'll also need to create a `~/.pip/pip.conf.tmpl`.  If you have an existing
`pip.conf` file, just rename it to `~/.pip/pip.conf.tmpl`.  Here's an example:

    [global]
    index-url = https://username:{{ get_password('pip', 'username') }}@pypi.example.com/simple/

If need multiple passwords in your `pip.conf`, such as a password protected
index-url and a password protected extra-index-url, you can repeat these steps
and make multiple `get_password` calls in your `pip.conf.tmpl`.  Just make sure
name of your Keychain items are unique and referenced properly in your
`pip.conf.tmpl`.

## Usage

Simply call spip as you would with pip.  For instance:

    spip install django

This should also work on Linux via the Secret Service API, i.e. GNOME Keyring
and KWallet, but this is untested.
