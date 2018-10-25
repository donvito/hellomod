This is a sample go module to demonstrate new functionality in Go v1.11

# Introduction to Go Modules in Go v1.11, Goodbye GOPATH!

When you start learning Go, it takes some time to understand how to setup your development environment. For me, there was a bit of a learning curve to understand how the compiler works in terms of searching for dependencies. 

One mandatory step you need to do when setting up your development environment, is configure the GOPATH environment variable. $GOPATH is used by the Go compiler to search for dependencies when building your Go application. $GOPATH contains source code and binaries.

GOPATH contains high-level directories below:
* $GOPATH/src - contains all your Go source code and third party source code dependencies
* $GOPATH/pkg - contains packages 
* $GOPATH/bin - contains binaries 

In the latest major release [Go v1.11](https://golang.org/doc/go1.11), GOPATH is no longer mandatory. The Go team has introduced [modules](https://github.com/golang/go/wiki/Modules) in this new version. Modules are a collection of related Go packages. This is a major step the Go team has taken to improve package management in Go.

With the current version 1.11, you can now build your Go application outside the GOPATH. In 1.11, when you place your source code inside $GOPATH, it ignores the modules feature and uses GOPATH to search for source code dependencies. However, if you place your source code outside the GOPATH, modules support is automatically enabled. So this means, you can now build your application from any directory you would like! 

I created a sample go module which you can use to play around with. Here's the source code https://github.com/donvito/hellomod. Feel free to fork the repo and use for your own learning. The module has 3 [releases or versions](https://github.com/donvito/hellomod/releases). 

![Screenshot-2018-10-07-20.11.02](/content/images/2018/10/Screenshot-2018-10-07-20.11.02.png)

To demonstrate how to use go modules in your application, let's assume that hellomod is a third-party module you require in your application.

Let's create a client application to use the [hellomod module](https://github.com/donvito/hellomod/). Here is some code we can start with. Save this as main.go in any directory you wish.

**main.go**
<script src="https://gist.github.com/donvito/5ca17d78bb651a8c5fb8718f2cc0e28a.js"></script>

Initialize module support. Make sure GOPATH is not set so we can automatically use the modules feature in Go 1.11. Execute the command below in the terminal. 
```
$ go mod init hello
```

This will create a go.mod file. Nothing to see here for now except the module name you just initialized. go.mod will contain
```
module hello
```

After executing "go mod init hello", build your application
```
$ go build 
```

What "go build" does is resolve the dependencies of your application based on your import statements and adds the latest versions of your dependencies, then compiles your application and produces the binary.

Since our latest version of hellomod is v1.0.1, **go.mod** now contains
```
module hello

require github.com/donvito/hellomod v1.0.1
```

"go build" also creates a go.sum file which contains the expected cryptographic checksums of the content of specific module versions. Definition taken from here https://github.com/golang/go/wiki/Modules#releasing-modules-all-versions

```
github.com/donvito/hellomod v1.0.1 h1:tOU3KDwvrGW7QUHXYQnk6YUD+92aTsRmJItJTD9f4I4=
github.com/donvito/hellomod v1.0.1/go.mod h1:DuOSvrBCwGkjX0WS5ohdpC/q67fS0Mde3LEM+2jeHSw=
```

Here's a summary of the commands we've executed so far
![Screenshot-2018-10-07-14.39.59](/content/images/2018/10/Screenshot-2018-10-07-14.39.59.png)

You can run the binary created by "go build" to check if we are using hellomod v1.0.1.

```
$ ./hello
Hello World v1.0.1!!!
```

Let's say you wanted to revert to a previous version v1.0.0 of the hellomod module. You can just execute the command below. This will use the previous version. 
```
$ go get github.com/donvito/hellomod@v1.0.0
$ go build
$ ./hello
Hello World 
```

For reference, here is what v1.0.0 looks like
**hellomod v1.0.0**
```
package hellomod

import (
	"fmt"
)

//SayHello function
func SayHello() {
	fmt.Println("Hello World")
}
```

To upgrade to a major version, you need to change the version in your imports.

**main.go**
<script src="https://gist.github.com/donvito/fe8d4f8b1ccfb953f224764f37b1d485.js"></script>

Then build the application and run the binary **"hello"**.
```
$ go build
$ ./hello
Hello MelvinHello World v2.0.0!!!
```

Here's how your **go.mod** will look like after doing a "go build"
```
module hello

require (
	github.com/donvito/hellomod v1.0.0
	github.com/donvito/hellomod/v2 v2.0.0
)
```

Yes, it is possible to use different versions of the same module. Here is an example where we can see that the 2 different versions of a module can be used independently. You need to use an alias for each version to avoid clashes.

**main.go ( *client application using 2 different versions of the hellomod module* )**

<script src="https://gist.github.com/donvito/4d36f696c6286878d61b712025452924.js"></script>



That's it! Hope you learned something from this article! :)

*For more updates, you can follow my blog and Twitter [@donvito](https://twitter.com/donvito). Most of my tweets are about Docker, Kubernetes, GoLang, Cloud, DevOps, Agile and Startups. I also share code in my [GitHub](https://github.com/donvito). If you want to know more about what I do, please add me in [LinkedIn](https://www.linkedin.com/in/melvinvivas/). I recently started a new [youtube channel](https://www.youtube.com/channel/UCi6RVSV8s9Yy2Qg3WcGq9cg) - I upload some tutorials there. Check it out!*
