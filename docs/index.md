% pacman-mirrors(8) Pacman-Mirrors 4.3 User Manual
%
% October, 2017

# NAME

pacman-mirrors - generate pacman mirrorlist for Manjaro Linux

# SYNOPSIS

pacman-mirrors [-f *NUMBER* | [[-i [-d]] [-c *COUNTRY*, [*COUNTRY*] ... | --geoip]]] [-m *METHOD*] [-a [-p *PREFIX*] [-R] [-G | -S *BRANCH*] [-P *PROTO* [*PROTO*] ...][-U *URL*]] [-b *BRANCH*] [-t *SECONDS*] [-q] [-v] [-n | -y]


# DESCRIPTION

Generate mirrorlist for Manjaro Linux. Default is to rank all mirrors by reponse time. If no arguments are given pacman-mirrors lists available options. Pacman-mirrors requires access to files which are read-only so it must be run with su or sudo. To create a up-to-date mirrorlist using all default use,

    pacman-mirrors --fasttrack 10 && sudo pacman -Syy

The mirrorlist generation process can be refined through arguments and arguments with options, for example,

    pacman-mirrors --country Denmark --timeout 5

## IMPORTANT
After all operations **ALWAYS** syncronize your pacman database with

    sudo pacman -Syy

# OPTIONS

Some options are mutual exclusive and will throw an arguments error:

* **\--no-mirrorlist** and **\--sync**
* **--branch**, **--get-branch** and **--set-branch**
* **--sync** and **--no-mirrorlist**
* **--country** and **--geoip**

Others can be used together but they have precedence. If the fasttrack arg is used with interactive, country or geoip the fasttrack arg will have precendence and the others are ignored. Some arguments requires other argument to have effect for example,this command will ignore --default argument

    WRONG pacman-mirrors -b unstable --default

as it should have been in conjunction with --interactive, like

    CORRECT pacman-mirrors -b unstable --interactive --default

The same goes for the API specific arguments. For those to have effect the --api argument must be present also.

    pacman-mirrors -aS unstable

The arguments can appear in any order except for arguments which takes additional options in which case the options must follow immediately after the argument, for example

    pacman-mirrors -aidS unstable

Pacman-mirrors downloads the lastest available status from [http://repo.manjaro.org](http://repo.manjaro.org). These data is always used during mirrorlist generation to ensure that you connected to a mirror which is up-to-date for your selected branch. Should you decide to temporarily switch branches you will still connect to an up-to-date mirror.

## IMPORTANT TO REMEMBER
It cannot be stressed enough that for every mirrorlist generation you commence, you **MUST** run *pacman-mirrors -Syy*.  

## YEE BE WARNED: Deprecated arguments
-g, \--generate 
:   The argument is deprecated in favor of **-f/--fasttrack**

-y, \--sync
:   An argument which never should have been here. The better option is to educate yourself to always use **pacman -Syy** after any change to your mirrorlist

## METHODS
-f, \--fasttrack *NUMBER*
:   Generates an up-to-date mirrorlist for the users current selected branch,
    mirrors are randomly selected from [http://repo.manjaro.org/status.json](http://repo.manjaro.org/status.json),
    the randomly selected mirrors are ranked by their current access time.
    The higher number the higher possibility of a fast mirror.
    If the number 0 is used - it is understood that all mirrors mirrors should be used.

-i, \--interactive [--default]
:   This is a function designed to leave full control for mirrors and protocols.
    This function **DOES NOT** take into consideration up-to-date mirrors. 
    The addition argument **--default** forces pacman-mirrors to load the default mirror
    file and ignore any preset custom-mirrors file, thus allowing for reselecting mirrors 
    for a new custom mirror file. 

-m, \--method *METHOD*
:   Default method is *rank* but *random* can be selected.

## BRANCH

-b, \--branch *BRANCH*
:   Temporarily use another branch, *stable*, *testing* or *unstable*.
    The branch is reset with next run of pacman-mirrors.

## COUNTRY
-c, \--country *COUNTRY* [*COUNTRY*] ...
:   Specifiy a country or a list of countries.

\--geoip
:   Use geolocation if possible, if geoip is not available all mirrors.

-l, \--list, \--country-list
:   Lists available mirror countries.

## API

-a, \--api [-p *PREFIX*] [-R] [-G|-S *BRANCH*] [-P *PROTO* [*PROTO*] ...] [-U *URL*]
:   Instructs pacman-mirrors to activate processing of API arguments

-p, \--prefix *PREFIX*
:   Add a path prefix to pacman-mirrors file-handling
    eg. */mnt/install* or *$mnt*

-G, \--get-branch
:   Return branch from configuration.

-P, \--proto, \--protocols *PROTO* [*PROTO*] ...
:   Write protocols to configuration,
    use *all* or *http*, *https*, *ftp* and *ftps*

-R, \--re-branch
:   Replace branch in mirrorlist

-S, \--set-branch *BRANCH*
:   Replace branch in configuration,
    use *stable*, *testing* or *unstable*

-U, \--url *URL*
:   Replace mirrorlist with supplied url

## MISC

-h, \--help
:   Show the help message

-n, \--no-mirrorlist
:   Use to skip generation of mirrorlist

-q, \--quiet
:   Make pacman-mirrors silent

-t, \--timeout *SECONDS*
:   Change the number of seconds waiting for a server response,
    SSL enabled mirrors has this value doubled to compensate
    for the time spent on exchanging encryption keys

-v, \--version
:   Show the version of pacman-mirrors

## Exit status:

    0     : OK
    1     : Problem with argument
    2     : Problem accessing systemfiles
    3     : Missing mirror file
    BRANCH: Value from config

## IMPORTANT TO REMEMBER
It cannot be stressed enough that for every mirrorlist generation you commence,
you **MUST** run *pacman-mirrors -Syy*.  

## Configuration flow of pacman-mirrors

At launch an internal default configuration is setup,
file configuration is applied and the commandline is parsed and applied.

## API arguments

These arguments modifies key elements of pacman-mirrors configuration
according to the packagers needs.

The actions performed by the API are in strict order and
performed *before any* other actions. This also means that ordinary arguments
supplied in conjunction with app might be ignored. Eg. **-U** argument terminates
pacman-mirrors when branch and mirrorlist has been written.

1. If *-G*
   * print *config.branch*
   * *sys.exit(0)*
2. If *p*  *PREFIX*
   *  add *PREFIX* to internal file configuration
3. If *-S* *BRANCH*
   * apply *BRANCH* to internal configuration
   * replace branch in pacman-mirrors.conf with *BRANCH*
4. If *-U* *URL*
   * apply internal configuration to a mirrorlist with *URL*
   * *sys.exit(0)*
5. If *-P* *PROTO* [*PROTO*] ...
   * replace protocols in pacman-mirrors.conf with *PROTO*
6. If *-R*
   * replace branch in mirrorlist with *-S* *BRANCH*

When done pacman-mirrors checks the internet connection and if possible
download the latest datafiles for creating the mirrorlist.
At this point it is possible to interrupt further processing.

If the *-n* argument is present pacman-mirrors will now exit.

# EXAMPLES

Most optional arguments are self explaining others require explanation.
The API functions is mainly designed to help packagers and iso-builders.
However it can be of use for everyone because it takes the hazzle out
of editing your pacman-mirrors configuration.

## IMPORTANT TO REMEMBER
It cannot be stressed enough that for every mirrorlist generation you commence,
you **MUST** run *pacman-mirrors -Syy*.

* Which countries has mirrors?

    *sudo pacman-mirrors -l*

* I want to temporary change branch to unstable, use geolocation,

    *sudo pacman-mirrors -b unstable --geoip*

* I want to permanently change branch to unstable, use mirrors from Germany and France, use only https and http protocol in that order

    *sudo pacman-mirrors -ac Germany,France -S unstable -P https http*

* Create a mirrorlist with German mirrors

    *sudo pacman-mirrors -c Germany*

* If you want more countries in your mirrorlist add them

    *sudo pacman-mirrors -c Germany France Denmark*

* Create a mirrorlist with 5 mirrors up-to-date on your branch

    *sudo pacman-mirrors -f 5*

* I want to choose my mirrors

    *sudo pacman-mirrors -i*

* I have a custom mirror list and I want to create a new custom mirror list?

    *sudo pacman-mirrors -i --default*

* I have a custom mirror list - can I reset it?

    *sudo pacman-mirrors -c all*

* What branch am I on

    *sudo pacman-mirrors -aG*

* Change system branch and dont change the mirrorlist

    *sudo pacman-mirrors -naS unstable*

* Change system branch and replace branch in mirrorlist and quit

    *sudo pacman-mirrors -naRS unstable*

* Change protocols you will accept but dont touch the mirrorlist

    *sudo pacman-mirrors -naP https http*

* A packager can write directly to a mounted systems datafiles using either a path or an environment variable replacing the branch in both configuration and mirrorlist leaving the mirrors as is

    *sudo pacman-mirrors -anR -p $prefix -S $branch -P https*

* It is also possible to specify a mirror in which case the mirrorlist is created and pacman-mirrors terminate

    *sudo pacman-mirrors -ap $prefix -S $branch -U $url*

# REPORTING BUGS
   <https://github.com/manjaro/pacman-mirrors/issues>

# SEE ALSO

The pacman-mirrors source code and all documentation
may be downloaded from <https://github.com/manjaro/pacman-mirrors/archive/master.zip>

# AUTHORS

    Esclapion <esclapion@manjaro.org>  
    philm <philm@manjaro.org>  
    Ramon Buldó <rbuldo@gmail.com>  
    Hugo Posnic <huluti@manjaro.org>  
    Frede Hundewadt <echo ZmhAbWFuamFyby5vcmcK | base64 -d>  
