### Update r2 stuff
clone git and run:
```
./sys/install.sh
```
### Update a given plugin (r2frida)
```
r2pm -ci r2frida
```

### Update All R2 Plugins
```
r2pm -i `r2pm -l`
```

### Update frida and tools
```
sudo pip3 install --upgrade frida
sudo pip3 install --upgrade frida-tools
```


## Frida
Full JS API at: https://frida.re/docs/javascript-api/

### iOS commands

Spawn app: 
```
frida -U -f <bundle id> --no-pause
//--no-pause is optional
```

Attach to running app:
```
frida -U <app name>
```

Load a script:
```
frida -U <app_name> -l <script>
```

List loaded classes
```
ObjC.enumerateLoadedClasses({
  onMatch(name, owner) {
    console.log('onMatch:', name, owner);
  },
  onComplete() {
  }
});
```
List a class methods:
```
ObjC.classes["NSString"].$ownMethods
```

Get the arg types of a given method:
```
//For a class named: "House" and a method named "- openDoor":
ObjC.classes.House['- openDoor'].argumentTypes
```

Get the return type of a given method:
```
//For a class named: "House" and a method named "- openDoor":
ObjC.classes.House['- openDoor'].returnType
```

Get a live instance of a class:
```
For a class named: "House":
var objInst=ObjC.chooseSync(ObjC.classes.House)[0]
```

Or create a new instance of a class:
```
For a class named: "House":
var objInst = ObjC.classes.House.alloc().init();
```

Call a method from the obj instance (previously created):
```
For a class named: "House" and a method named "- openDoor":
objInst["- openDoor"](paramValue)

or

objInst.openDoor(paramValue)
```

Instrumenting a method:
```
//For a class named: "House" and a method named "- openDoor":
    // As this is an Objective-C method, the arguments are as follows:
    // 0. 'self'
    // 1. The selector (openDoor:)
    // 2. The first argument to the method (if any)
Interceptor.attach(ObjC.classes.House['- openDoor'].implementation,
{
  onEnter: function (args) {
  	var arg = ObjC.Object(args[2]).toString();
	   send('First argument is ' + arg);
  },
  onLeave: function (retval) {
  		var ret = ObjC.Object(retval).toString()
  	 	send('return value is ' + ret);
  }
});
```

Tracing a Native Function:
```
// List Modules
Process.enumerateModules()

//get specific module and enumerate its exports
var mod = Process.enumerateModules()[1]
mod.enumerateExports()

//For a library called: houseNative.dylib and a function called "openDoorNative"
	// The following finds the address in memory of the function
var funcPtr = Module.findExportByName('houseNative.dylib', 'openDoorNative');

Interceptor.attach(funcptr,
{
  onEnter: function (args) {
  	console.log("Function Executed ")
  	console.log("Args: "+ ObjC.Object(args[0]))
  },
  onLeave: function (retval) {
  	console.log("Retval: "+ retval)
  }
});
```

Calling a Native function:
```
//For a library called: houseNative.dylib and a function called "openDoorNative"
	// The following finds the address in memory of the function
var funcPtr = Module.findExportByName('houseNative.dylib', 'openDoorNative');

	// The following creates the native function
	// first param is pointer (NativePointer)
	//second param is return type
	// Third param is an array of arguments for the function
var myFunction = new NativeFunction(funcPtr, 'char', ['pointer']);
```

## Objection commands

Start objection attaching to given app:
```
objection -g <appName> explore
```

Spawn an app from objection and execute early instrumentation command (disable ssl pinning for ios -  You can execute multiple commands):
```
objection -g <bundle name> explore --startup-command 'ios sslpinning disable'
```

List Bundles:
```
ios bundles list_bundles
```

List Frameworks:
```
ios bundles list_frameworks
```

List classes:
```
ios hooking list classes
```

List methods from class:
```
ios hooking list class_methods <class name>
```

Searching for classes:
```
ios hooking search classes <class name>
```

Searching for methods:
```
ios hooking search methods <search term>
```

Changing a method's boolean return value (ONLY WORKS FOR BOOLEANS)
```
ios hooking set return_value "-[<class name> <method name>]" false
```


Tracing all methods of a class:
```
ios hooking watch class <class name>
```

Tracing a specific method:
```
ios hooking watch method "-[<class name> <method name>]" --dump-args --dump-return --dump-backtrace
```




Generating simple frida hooks:
```
ios hooking generate simple <class name>
```

Get app's shared cookies:
```
ios cookies get
```

Search for live instances of a particular class
```
ios heap search instances <class name>
```

Jailbreak disable/simulation:
```
ios jailbreak disable/simulate
```

### Keychain

Dump keychain:
```
ios keychain dump
```


### Monitor the pasteboard

```
ios pasteboard monitor 
```


## R2Frida commands


### Starting iOS
```
 -> Spawn app: (Note that the app's bundle identifier is used)
r2 frida://spawn/usb//<bundle id>

 -> Spawn an app and run a frida script as early instrumentation
r2 -c 'script.js' frida://spawn/usb//<bundle id>


 -> Attach to running app: (note that you can use the app name)
r2 frida://attach/usb//<app name>

```

### Basic Commands


 -> Get arch/bits info from the target side (.\i* to import it into r2):
 ```
\i 
```
 -> List imports:
 ```
\ii[*]
```
 -> List libraries loaded in the app process:
 ```
\il
```
 -> List symbols of lib (local and global ones):
 ```
\is[*] <lib>
```
 -> List symbols of lib but only export global ones:
```
\iE[*] <lib>
```
 -> Show address of symbol:
```
\isa[*] (<lib>) <sym>
```
 -> show entrypoint:
```
\ie
```
 -> List Objective-C classes or methods of <class>:
```
\ic <class>
```
 -> List Objective-C protocols or methods of <protocol>:
```
\ip <protocol>
```
 -> Inverse symbol resolution:
```
\fd[*j] <address>
```


### Tracing

 -> Trace the list of registers specified when executing addr
```
\dtr addr reg0 reg1
```
 -> Trace address with format (^ixzO)
```
\dtf <addr> [fmt]

// Example:
// Tracing ptrace(int, int, int, int)
\dtf ptrace iiii
```
 -> Trace list of addresses or symbols
```
\dt (<addr>|<sym>)
```
 -> Trace at current offset
```
\dt.
```
 -> Show memory regions
```
\dm[.|j|*]
```
 -> Clear all tracing
```
\dt-
```
 -> Tracing all functions of a given class:
```
\dtf $$ @@=`\ic* <class_name>~[2]`
```
 -> Tracing all functions of a given framework:
```
 // The $$ serves as the \dtf parameter. And it means at current offset
 // The @@= iterates the addresses returned by the \iE command
 \dtf $$ @@=`\iE* <frameworkName>~sym.fun[2]`
```
### Debugging Commands

 -> List or place breakpoint:
```
\db (<addr>|<sym>)
```
 -> Remove breakpoint(s):
```
\db- (<addr>|<sym>)|*
```
 -> Continue breakpoints or resume a spawned process:
```
\dc
```
 -> start the chrome tools debugger:
```
\d.
```

### Instrumentation
```
 -> Call the target symbol with given args:
\dxc [java:|sym|addr] [args..]

For example:
\dxc <addr> "1234"

 -> Intercept and replace return value of address
\di[0,1,-1] [addr]
 -> Run Frida script
\. script.js
 -> Execute Frida inline command
\eval <frida command>

For example:
To Inspect the ObjC UI KeyWindow from r2frida
\eval ObjC.classes.UIWindow.keyWindow().recursiveDescription().toString()
```
### Search
```
 -> Search hex/str pattern in mem ranges (see search.in=?)
\/[x][j] <string|hexpairs>
 -> Search wide string
\/w[j] string
 -> Search for a value (honors `e cfg.bigendian`)
\/v[1248][j] value
```

##R2
### Start an empty r2 session
```
r2 -
```

### Start r2 with ALL the analysis
```
r2 -A <file>
```

### Tell r2 to ignore the nops at the beginning of functions
```
e anal.nopskip = false
```

### Print disassemble
```
 -> print 10 instructions:
pd 10

 -> print function
pdf
```


### Look for references for a given symbol
```
axt <symbol>

For example:
dxt sym.imp.dlsym
```

### Open Visual mode:
```
V

 -> Press "p" to switch between visual modes

```
### Rename current offset with automatic suggestion
```
.afna
```

### Analyze functions:
```
 -> Analyze function at address
af <address>

 -> Analyze functions recursively:
afr <address>
```

### Rahash
```
 -> List all the crypto/hash algorithms supported by r2
rahash2 -L


 -> Generate all the hashes for the given string
rahash2 -a all -s <string>
