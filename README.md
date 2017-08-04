# idea-subdirs-issue
Simple project to demonstrate IDEA's inabillity to deal with **gradle excludes and nested source sets**.

The project structure is extremly simple: there are two implementations of the same `toplevel.A` class, in two different source sets, `main` and `subd`. The peculiarity: `subd` sources are nested under `main` sources.

```
---src
   \--- main
        \--- java  # <-- sourceSets.main.java.srcDirs
             \--- toplevel
                  +--- (toplevel.)A.java
                  \--- subd # <-- sourceSets.subd.java.srcDirs
                       \--- toplevel
                            \--- (toplevel.)A.java
```


In gradle, this is a no-brainer: first exclude `subd` from the main sources:

```gradle
sourceSets {
    main {
        java {
            exclude '/toplevel/subd/**'
        }
    }
```

and then declare the `subd` source set:

```gradle
sourceSets {
    subd {
        java {
            srcDirs = [ 'src/main/java/toplevel/subd' ]
        }
    }
}
```

The result is that gradle compiles both source sets into different classes dirs. For instance, with gradle 4.0.2:

---build
   \--- classes
        +--- main  # <-- sourceSets.main.java.srcDirs
        |    \--- toplevel
        |         \--- (toplevel.)A.class
        \--- subd  # <-- sourceSets.subd.java.srcDirs
             \--- toplevel
                  \--- (toplevel.)A.class
```
