# podtools / podget

A simple podcast downloader in Go, and an accompanying library.

I needed a command line utility to download and archive my favorite podcasts.
I tried various existing utilities, but they were all defective in some way or
hard to set up, so I resorted to writing my own. I wanted to be able to run it
as a scheduled task on my Synology box, so the easiest thing to do was write it
in Go. As a side effect, it was easy to make it multithreaded.

Example:

    podget -d ~/TAL -r 30 -v http://feed.thisamericanlife.org/talpodcast

The `-r 30` means that if a file exists already but is more than 30 days 
old, we assume they're doing a rerun and download the new version.

The `-d ~/TAL` argument specifies the destination directory.

The `-v` says to be verbose and output progress messages.

## Podtrac

Increasingly, podcasters have started to use podtrac.com, a commercial service which
tracks download statistics. Unfortunately, this breaks easy downloading of podcasts,
because the filename always ends up being `default.mp3`. So, a special argument `-podtrac`
has been added to allow you to specify some way to extract the episode number from the podcast
metadata.

For example:

    podget -d ~/TAL -r 30 -v -podtrac 'item.description /^(\d+):/' http://feed.thisamericanlife.org/talpodcast

The argument syntax is a fieldname, then a space, then a regex in `//` with a capturing group. The available
fields are:

```
item.author
item.category
item.description
item.duration
item.guid
item.pubDate
item.title
enclosure.url
```

## Building

Clone this repository, `cd` into the same directory as this README.md file, and then:

    go build ./cmd/podget

You should end up with an executable in the current directory.

You can cross-compile [in the usual Go
way](https://dave.cheney.net/2015/08/22/cross-compilation-with-go-1-5). For
example, to build a binary for your Linux Synology box using your Mac,

    GOOS=linux GOARCH=amd64 go build ./cmd/podget

