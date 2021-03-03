## Objection commands

### Starting

Start objection attaching to given app:
```
objection -g <appName> explore
```

Spawn an app:
```
objection -g <Bundle_ID> explore
```

Spawn an app from objection and execute early instrumentation command (disable ssl pinning for ios -  You can execute multiple commands):
```
objection -g <bundle_ID> explore --startup-command 'ios sslpinning disable'
```
## Recon

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

List loaded modules:
```
memory list modules
```

List exports from a specific module:
```
memory list exports <module_name>
```

Search for live instances of a particular class
```
ios heap search instances <class name>
```


## Tracing

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

Changing a method's boolean return value (ONLY WORKS FOR BOOLEANS)
```
ios hooking set return_value "-[<class name> <method name>]" false
```


## File Management

Print a file's content:
```
file cat <filename>
```

Print a Plist:
```
ios plist cat <filename>
```

Download a file:
```
file download <remote_filename> <local_destination>
```

Upload a file:
```
file upload <local_filename> <remote_destination>
```

Start HTTP file server:
```
file http start
```

Stop HTTP File server
```
file http stop
```

## Misc

Get app's folders:
```
env
```

Get app's shared cookies:
```
ios cookies get
```
### SSLPinning
Disable SSLPinning
```
ios sslpinning disable
```

### Jailbreak detection
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