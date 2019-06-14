# Building An App with Go Modules, GoCenter and Athens :tada:

Hey Gophers! We're gonna build an awesome webapp with [Gin](https://github.com/gin-gonic/gin). Well, it's pretty basic but it shows cat and dog pictures so it's still pretty awesome :grinning:.

The cool part though? Instead of pulling all my webapp's dependencies directly from version control systems like GitHub (which we've always been doing in the past), we're gonna build it using **module repositories**. In this demo, we'll show off [GoCenter](https://gocenter.io), [Athens](https://docs.gomods.io), and then both in action!

We're going to build our app three different ways, and we'll talk about each one.

>I gave this demo at [SwampUp 2019](https://sched.co/Nd9W) in the session titled "Improving Dependencies for Everyone with Go Modules and Module Repositories"

![athens banner](https://d33wubrfki0l68.cloudfront.net/517b1a47a4e75a20cbddd06e936d73837d3c2297/c84eb/banner.png)

# About the Web App

The web application we're going to build is pretty standard. I built a little server using [gin](https://github.com/gin-gonic/gin) as the framework, and it shows some HTML pages showing pictures of cats and dogs.

The cool thing is that the whole codebase has the new modules dependency system enabled on it. Doing that was fairly simple - I ran `go mod init` and then `go build`.

The `go.mod` file keeps track of the modules that my app directly uses (not the transitive dependencies), and the `go.sum` file holds checksums of all the modules that my app requires, both direct and transitive.

>The `go.sum` file means that if a module's code changes upstream in version control, the build will fail!

# Run The Demo

Below is how how to do the demo yourself. The instructions are for UNIX shells. They've been tested with Bash and ZSH.

## Demo 1: Build Using GoCenter

Let's first build the code by telling our `go` tool to use GoCenter:

```console
$ export GOPROXY=https://gocenter.io
```

>GoCenter is a publically available, stable module repository that holds the most widely used public Go modules. You can use it to build your code as long as you don't rely on any private modules. If you do, read on!

### Clear Local Module Cache

The `go` toolchain keeps a local on-disk cache of every module & version it's ever downloaded, so we need to clear the cache before we do any of the builds in these demos.

Run this command to clear the cache:

```console
$ sudo rm -rf $(go env GOPATH)GOPATH/pkg/mod
```

### Build & Run The Server!

`go run` has always been the standard way to build and then immediately run your code. With the new modules system, nothing changes. Execute this command to run the server:

```console
$ go run .
```

>After you run the server, open your browser up to `http://localhost:8080` to see the dog and cat pictures!

## Demo 2: Layer Athens on GoCenter

I mentioned that you can't use GoCenter to build your apps that depend on private code. Similar to how you would use Artifactory to host your own private assets, you can use [Athens](https://docs.gomods.io) to host your private Go modules. 

>Artifactory supports private Go modules too! Athens is an open source private Go module repository, but if you're looking for a commercially-supported private Go module repository, go check it out.

We're going to layer Athens on top of GoCenter to:

1. Allow us to build our app using private module dependencies
1. Still take advantage of the fast and efficient GoCenter public module repository 

It's pretty easy to run your own Athens. It's a free (as in beer) open source project and we provide [instructions](https://docs.gomods.io/install) for running the server in a few different configurations. Today, we're going to use [Docker](https://www.docker.com/) to run ours.

First, run this to start Athens up:

```console
$ docker run \
    -e ATHENS_DOWNLOAD_MODE=sync \
    -e ATHENS_DOWNLOAD_URL=https://gocenter.io \
    -e GO_ENV=development \
    -p 3000:3000 \
    arschles/athens:swampup2019
```

And then to set your environment variable to point to the local server instead of GoCenter.

```console
$ export GOPROXY=http://localhost:3000
```

>After you change your `GOPROXY` variable, your `go run` commands will only try to download module dependencies from the Athens server that you run! That's is really useful because you can configure Athens to access your private repository. It has a whole host of other features too, such as its own immutable database and the ability to filter out upstream public modules that your organization can't use. See [here](https://docs.gomods.io/intro/why/) for a little more on how Athens fits into your organization.

Before you do your second build, don't remember to clear your cache just like last time:

```console
$ sudo rm -rf $(go env GOPATH)pkg/mod
```

Now, you're rerady to start up the server again! Don't forget to shut down the old one (with ctrl+C) and then run:

```console
$ go run .
```

## Demo 3: Use Your Athens While Offline :scream:

Like I mentioned in the last step, you can run your own Athens server to build your apps with your own private modules. Athens is tailor made to be run inside of organizations that have private code and their own restrictions on what _public_ code they are willing to use, and how they access it.

Athens keeps its own database of _all_ the modules you request from it - public and private. This database is **write only**, which means that once you ask Athens for a module and it serves it up, that module is stored forever and will never change.

This powerful database means that you can _cut Athens off from the internet_ and still build your apps with all the same performance benefits.

>Athens can be used inside of organizations that have completely firewalled off access to the internet

So, let's get started! The first step is the easiest - keep the Athens server from last time running.

Next, clear out your cache again:

```console
$ sudo rm -rf $GOPATH/pkg/mod
```

And then, **shut down your internet connection** :see_no_evil:.

And finally, do the build & run:

```console
$ go run .
```

And you're done!

## Finally

Thanks for following along! Now you're both a Gopher _and_ an Athenian :green_heart:.

If you want to learn more, check out [docs.gomods.io](https://docs.gomods.io)! We'd also love for you to get involved - here are some ways to do so:

- Come star [our repo](https://github.com/gomods/athens)
- Come say hello on the `#athens` channel in the [Gophers Slack](https://invite.slack.golangbridge.org/)
  - This is a great place to come ask for help getting started and ask questions too :smile:
- Come to one of our [developer meetings](https://docs.gomods.io/contributing/community/developer-meetings/). Absolutely everybody is welcome, regardless of experience, background or anything else
- And of course, file [bug reports](https://github.com/gomods/athens/issues/new/choose) or [feature requests](https://github.com/gomods/athens/issues/new/choose) and [contribute code](https://docs.gomods.io/contributing/new/development/)!

# Keep on rockin', Gophers!

![athens gopher](./athens-gopher.png)
