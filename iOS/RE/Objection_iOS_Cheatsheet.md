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

## Misc

Get app's shared cookies:
```
ios cookies get
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