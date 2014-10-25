---
layout: post
title: "Debugging Ruby C Extensions in XCode"
date: 2014-08-06 21:14
comments: true
categories: [ruby,xcode]
---

Having a nice user interface to set breakpoints, step through execution and
inspect local variables can be just what you need when chasing a hard to fix
bug in your Ruby C extension. If you're on OSX you're in luck, because this
post is about how you can import your C extension code as an XCode project
and enjoy the benefits of the debugger and other developer tools.

*Note: I'm using XCode v5.1.1 in this tutorial*

## 1. Get the code

First of all you need to get the code you want to debug. For this example,
I'm going to use [Rbkit](https://github.com/code-mancers/rbkit), a Ruby profiler
which comes with a C extension. I've cloned the gem from its github repo
( Repo: `https://github.com/code-mancers/rbkit.git` Revision: `04212f93777e7b90fc528b7b1566` )
and run `ruby extconf.rb` inside `ext` directory to create the Makefile. XCode
can take it from here.

## 2. Create a new XCode project

Select `File -> New -> Project` and choose `External Build System` from `Other` :
![XCode New Project](/assets/img/xcode/new_project.png)

Fill in some product code, say rbkit:
![XCode rbkit project](/assets/img/xcode/rbkit_project.png)

Select the `ext` directory of the C extension, this is where the XCode
project will be created :
![XCode ext directory](/assets/img/xcode/ext_directory.png)


## 3. Build the project

Now the project is set up and a "Target" is created which is used to build
the project. Under `External Build Tool Configuration` (which should be there
on your screen at this point), choose the `ext` directory of Rbkit as the
directory and uncheck `Pass build settings in environment` checkbox :
![XCode target](/assets/img/xcode/rbkit_target.png)

Now you should be able to press <kbd>&#8984; cmd</kbd>+<kbd>B</kbd> and build
the project successfully.

## 4. Prepare an executable

You'll need to give XCode an executable so that it can be run using
a debugger. For us the executable is going to be the "ruby" executable.

Create a symlink to ruby at `ext` directory :

```bash
~> ln -s `which ruby` ruby
```

Now we'll configure the "Run" command of XCode so that it runs our `ruby`.
Select `Product -> Scheme -> Edit Scheme`. For "Run" scheme (which you can
select on the left hand side), under `Executable`, select `Other` and choose
the `ruby` symlink:
![XCode ruby executable](/assets/img/xcode/choose_executable.png)

Select `Options` tab and enable `Use custom working directory` and choose
`ext` as the working directory:
![XCode working directory](/assets/img/xcode/current_working_directory.png)

Select `Arguments` tab and click on `+` under `Arguments Passed On Launch` to
add the following argument to ruby executable : `../experiments/using_rbkit.rb`:
![XCode ruby arg](/assets/img/xcode/ruby_arg.png)

Click OK.

Now you should be able to press the Run button
<img src="/assets/img/xcode/run.png" style="width:25px;height:20px;padding:0px;margin-bottom:-5px;">
( or <kbd>&#8984; cmd</kbd>+<kbd>R</kbd> ) to run the script using Ruby.
Once the script is running, you should be able to see that Memory usage keeps
increasing steadily under the Debug Navigation (<kbd>&#8984; cmd</kbd>+<kbd>6</kbd>).
That's because our `experiments/using_rbkit.rb` script just keeps creating
new objects in Ruby.
<img src="/assets/img/xcode/debug_nav.png" style="max-height:500px;max-width:400px;align:middle;">

## 5. Start debugging

Now that the project is set up and we're able to run the code using XCode,
we should be able to map the source files for the executables and use the
debugger to add breakpoints and step through the code.

#### 1. Add source files

Select Project Navigation ( <kbd>&#8984; cmd</kbd>+<kbd>1</kbd> ) , click the
`+` button on the bottom left corner and select `Add files to "rbkit"`.
![XCode add source files](/assets/img/xcode/add_source.png)
You can now add the source files which you want to debug. For now, I'm just adding
`rbkit_tracer.c`.

#### 2. Set breakpoints and inspect variables

Let's add a breakpoint on `rbkit_tracer.c:206` :
![XCode add breakpoint](/assets/img/xcode/breakpoint.png)

This line gets called everytime a new object is created to serialize the
object's classname. Once we "Run" the project, execution should pause at
this breakpoint. Now we can "Step into" the `pack_string` function
( using <kbd>F7</kbd>) to see the classname being passed into it. At the bottom
on the left half, we can see the variables and their values and on the right
half is an interactive lldb console. `frame variable string` will return the
value of the `char * string` argument of `pack_string` function where we have
stepped into. Check it out :
![XCode inspect variable](/assets/img/xcode/inspect_variable.png)

## Done!

Good job if you've reached this far. You should be able to take this forward
and unleash mad debugging skills upon all C extensions.

To recap :

1. We cloned the C extension source, created the Makefile.
2. Set up XCode project and built the project.
3. Ran our test script using the ruby executable from XCode.
4. Added source files and tried out some debugging.
