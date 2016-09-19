<!--TOC-->

# Table of Contents
* [Web site](#web-site)
  * [Content](#content)
    * [Regular Tasks](#regular-tasks)
  * [Using Git to maintain the web pages](#using-git-to-maintain-the-web-pages)
  * [Hints about Jekyll](#hints-about-jekyll)
    * [Required Tools](#required-tools)
    * [Looking at the website locally](#looking-at-the-website-locally)
    * [How to create a new post?](#how-to-create-a-new-post)
    * [How to create a new page?](#how-to-create-a-new-page)
    * [How to publish?](#how-to-publish)
    * [Notes from @bo0ts on cgal-develop, April 1st, 2015](#notes-from-@bo0ts-on-cgal-develop-april-1st-2015)
  * [Hosting](#hosting)
  * [Access statistics](#access-statistics)
  * [Releases download statistics](#releases-download-statistics)
* [`firstname.lastname@cgal.org`](#firstnamelastnamecgalorg)
* [Wiki](#wiki)

<!--TOC-->

## Website

### Content

-   Our webpages are <http://www.cgal.org>
-   The Editorial Board is responsible for the content. The upload is
    done automatically as soon as the `master` or `upcoming` branches
    are updated. Developers in the team
    [cgal-web-editors](https://github.com/orgs/CGAL/teams/cgal-web-editors)
    and members of the editorial board have write access the the
    cgal-web repository. [Regular
    developers](https://github.com/orgs/CGAL/teams/developers) have read
    access only, thus an edit should be done using a pull request.
-   Of course, issues can be submitted if you find any problems with the
    web site.

#### Regular Tasks

Some aspects of the web pages need constant attention to keep them
up-to-date. If you can please do the necessary updates yourself, or
notify the Editorial Board if you know about updates. This is relevant
in particular for the following web pages:

-   The News section on <http://www.cgal.org>
-   People involved in the project <http://www.cgal.org/people.html>
    (also observe that the Board Manager, the Release Manager, and the
    Review Manager are subject to change from time to time).
-   Updates for the work in progress page
    (http://www.cgal.org/in\_progress.html): work that is finished and
    thus no longer in progress must be removed, and new work must be
    added.
-   Publications related to CGAL appear at
    <http://www.cgal.org/bibliography.html>, which is currently
    maintained by [@moniqueteillaud](https://www.github.com/moniqueteillaud)
-   Projects using CGAL <http://www.cgal.org/projects.html>
-   Events listed on <http://www.cgal.org/events.html>
-   *(This item is not so clear, to be updated)* Demo programs to be
    included in the package overview
    <http://www.cgal.org/Manual/latest/doc_html/cgal_manual/packages.html>.
    See the Developers manual for how to submit these.


### Using Git to maintain the web pages

Clone the repository

``` {.bash}
> git clone -o cgal-web git@github.com:CGAL/cgal-web.git
```

If already done

``` {.bash}
> git pull
```

updates your current branch from the server. It's recommended not to
have local modifications when doing so.

Adding a new file

``` {.bash}
> git add <file>
```

Committing a file to your local repository

``` {.bash}
> git commit <file>
```

Next task is to send the updates to the server. In order to avoid pushes
in all branches use `git` (\>= 2.0.0) (where the default is to only push
the current branch) or add this to your configuration

``` {.bash}
> git config --add push.default simple
# In even older Git version (<1.7.11) 'simple' must be replaced with 'current' or 'tracking' (for even older versions)
```

This refuses a push if the local and remote branchnames do not match
(check whether other repositories also need this option - or whether you
want to add it to you global `.gitconfig` - can be done by adding
`--global` option). Next you send the branch onto the remote repository
she cloned from ("origin"):

``` {.bash}
> git push 
```

Actually, the change of the default push strategy is more important when
you are working on local branches other than `master`, see below.

You can also create a local branch to test some changes:

``` {.bash}
> git checkout -b NewDesign master
```

then you add/modify/commit files in the local "NewDesign" branch. Browse
the files locally to see your changes.

Once you're happy:

``` {.bash}
> git checkout master
> git merge NewDesign
## resolve conflicts
> git push
```

The last commands updates the server again.

If needed, usually to prepare a release, we have
<http://upcoming.cgal.org>

``` {.bash}
> git checkout -b upcoming cgal-web/upcoming
## make sure upcoming and master are the same
> git merge master
## resolve conflicts
> git merge NewDesign
## resolve conflicts
```

and finally

``` {.bash}
> git push
```

### Hints about Jekyll

#### Required Tools

-   Install Ruby
    -   Linux: check your package manager
    -   Windows
        -   Note: you should run everything in cmd.exe and not a cygwin
            shell.
        -   go to <http://rubyinstaller.org/downloads/> and download
            RubyInstaller 2.0.0 and the appropriate Development Kit
        -   execute RubyInstaller (check "Add to Path")
        -   unzip Development Kit somewhere
        -   in \`cmd.exe\`:
```
cd C:\path_to_devkit
ruby dk.rb init
ruby dk.rb install
```
-   Install jekyll: \`gem install jekyll\`
    -   If download error, try \`gem install jekyll --source
        <http://rubygems.org>\`

#### Looking at the website locally

```
# get the required tools
cd /path/to/cgal/web
jekyll serve --watch
# point browser at [http://localhost:4000](http://localhost:4000)
```

#### How to create a new post?
```
cd /path/to/cgal/web
rake post title="My Title" [date="2014-02-24"]
```

#### How to create a new page?

` cd /path/to/cgal/web`\
` rake page name="my_page.html"`

#### How to publish?

-   Using Push (if you are in the team
    [cgal-web-editors](https://github.com/orgs/CGAL/teams/cgal-web-editors))
    -   Push your changes to the `master` branch.

```
 git push origin your_branch:master
```

-   Using Pull Request (otherwise)
    -   Push your branch to your own repository and create a pull
        request to the \`master\` branch.

After a few minutes (sometimes hours), you will see the content here:
<http://www.cgal.org>

Another way to test the website online without perturbing cgal.org (if
you cannot test it locally, for example) is to push to the \`gh-pages\`
branch and watch the result here: <http://cgal.github.io/cgal-web/>

#### Notes from @bo0ts on cgal-develop, April 1st, 2015

There are only two short commands: `rake post` and `rake page`. They
are documented here:
<http://jekyllbootstrap.com/usage/jekyll-quick-start.html#toc_7>

A quick explanation: there are posts, drafts, and pages.

Pages are the sites with a fixes address, like software.html or
index.html. They contain not reusable contents and can be placed
anywhere in the repository hierarchy besides folders prefixed with `_`.

Posts are stored in the `_posts` directory, they have a date, tags, and a
category. A release announcement, for example, is a post. Exhaustive
documentation about posts is here: <http://jekyllrb.com/docs/posts/>

Drafts are posts without a date which are not published by the automated
tools, but you can see them locally. They are stored in the `_drafts`
directory. Exhaustive documentation about drafts is here:
<http://jekyllrb.com/docs/drafts/>

In general, <http://jekyllrb.com> contains a lot of helpful information.

### Hosting

**Update:** this has changed. See [`Staging Area/Servers/Migration to
OVH` *on the private wiki*](https://cgal.geometryfactory.com/CGAL/Members/wiki/Staging_Area/Servers/Migration_to_OVH). [@lrineau](https://github.com/lrineau) is responsible for the web site,
now, with [@sloriot](https://github.com/sloriot) as backup.


### Access statistics

CGAL Editors can access the statistics for <http://www.cgal.org/> and
<http://doc.cgal.org/> at the following address:
<https://logs.ovh.net/cgal.org/>

That page is protected by a login/password. The login is "editor", and
the password is the usual password for the pages protected for CGAL
editors. Be careful that the login is not exactly the usual login.

### Releases download statistics

For each new release of CGAL, the tarballs and the Windows `.exe` installer
are now uploaded to GitHub, as "assets".

See https://github.com/CGAL/cgal/releases

The download statistics can be seen at:

  http://www.somsubhra.com/github-release-stats/?username=CGAL&repository=cgal

## `firstname.lastname@cgal.org`

Since February 2015, GeometryFactory handles the service of email
redirections `firstname.lastname@cgal.org`, using the OVH
infrastructure. Changes to the configuration should be communicated to
[@lrineau](https://github.com/lrineau) (or [@sloriot](https://github.com/sloriot)) when [@lrineau](https://github.com/lrineau) is not available).

*For the administrators:* *The settings are in the [OVH Manager](https://www.ovh.com/manager),
select "cgal.org" in the list of
products in the left bar, then the "Emails" tab, then in the buttons on
the right, use "Gestion des redirections" ("Manage redirections" in
English).*

## Wiki

The internal wiki is hosted at
<http://cgal.geometryfactory.com/CGAL/Members/wiki/> .

It is administrated at GF by [@lrineau](https://github.com/lrineau).
