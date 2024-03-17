# Otter Scripting Language
A type-checked bytecode compiled scripting language. Inspired by rSquirrel. (a fork of Squirrel by Respawn)<br>
<br>
Otter is just a design as it is now. No actual implementation is ready.

## Why?
Personally, I hate Lua. The most popular scripting language out there. However in my opinion lua has some design choices I absolutely hate. When I started checking out what are the alternatives, I was quite shocked to see there are a few alternatives that are as popular as Lua. So there I am.

## What is my goal?
I am trying to create a syntax that should be easy enough to learn for new programmers and still have the similarity to other languages. Something between Lua and C-like syntax. The language itself will offer some API to easily embed VM to your projects and easily add functions, and variables to the VM. This is actually more of a personal project I want to do as I am planning to use my own scripting language on my own 2D game engine. However, you are free to use it however you like.

## Why Otter
They are cute.

## Syntax
Not a lot of examples will be here. Check out [examples](./examples) directory for that.

#### Variables
Otter's variables are type-checked. It is neccessary to specify your value type when creating a variable.

```cpp
int my_age = 18
```

```cpp
float money_i_have = 5.2
```

Unlike most languages, String is a primitive type for Otter.

```cpp
string message = "Hello, i am a new language"
```

##### Constants
To define a const variable use `const` keyword. 
```cpp
const string PROJECT_NAME = "Some Project"
```

##### Arrays
Otter's arrays are dynamic, they can grow as large as it needs to be.
```java
List<string> fruits = ["Apple", "Orange", "Banana"]
```
Otter arrays start at index 0 as it is conventional index.

##### Dicts
Otter provides a Python like dict feature.
```py
Dict<string, string> roles = {"Me": "Admin", "Cat": "Animal"}
```

##### Variable hoisting
Otter offers a basic variable hoisting feature.

```cpp
string data // null at this point

data = readIn()
```

##### Exporting variables
Otter allows multiple source files to be used. To share codes across files you can use `export` keyword
```
export projectName
export someFunction // Allows exporting functions
export someStruct // Allows exporting structs

string projectName = "Some Project"
```

##### Special Strings
This feature is a special feature of Otter API. Before reading files, you can create special character strings that will be converted to a specific type of variable on runtime. This will be likely an experimental feature. But there we go, since we don't have any actual implementation yet, I will use javascript to show a simple pseudo code.

```js
function assetString(path){
    return new AssetStruct(path)
}

let config = {
    "specialstrings": {
        "$": {
            "call": assetString,
            "return": AssetStruct // Needs to be registered to VM before hand.
        }
    }
}
```

Inside the code:
```
asset image = $"./Some/Path/To/File.png"
```

##### Vectors
Vectors are also a primitive type of the Otter.

```
vector velocity = <3, 3, 3>
velocity.x = 5
```

#### Structs
Structs are an easy way to bundle all of your variables. Similar to classes.

```
struct Car {
    int max_speed
    string name
    string version
}

Car car
print(car.max_speed) // 0
print(car.name) // ""
print(car.version) // ""

Car car = {max_speed = 200, name = "Bugatti"} // Create an instance with specified variables
```

Nesting structs are possible

```
struct World {
    string folder
    string name
}

struct Location {
    vector position
    World world
}

struct Player {
    string name
    int healt
    Location location
}

Player player = {name="Player123", healt=5, location={position=<5, 5, 5>, world={folder="./some/path/to/folder", name="Earth"}}}
```

##### Self Nesting Structs
Self-nesting structs are also possible however they need to be null initialized otherwise VM will try to create instances forever. (Not actually possible since this will fail before even running the code)
```
struct Node {
    Node child = null
}
```

##### Default Field Values
```
struct Cat {
    string name = "Alex"
    int age
}

Cat cat
print(cat) // Cat{name="Alex", age=0}
```

##### Singleton Instances
One instance structs are defined by specifying name after defining the struct.
```
struct {
    int brightness
} Config
```

#### Functions
Functions are defined with this syntax

```
fn isPositive(int value) -> bool {
    return value > 0
}
```

##### Functionrefs
Functions can be referanced using functionref.
```
fn isPositive(int value) -> bool {
    return value > 0
}

bool functionref(int) = isPositive

fn call(bool functionref(int) fn, int number){ // -> void
    fn(number)
}

call(isPositive)
```

##### Optional parameters
```
fn OR(bool a = true, bool b = true) -> bool {
    return a || b
}
```

##### Closures
It is possible to use closures in Otter

```
void functionref() fn = void fn(){
    print("Hello World!")
}

bool functionref(int) isPositive = fn(int val)->bool{
    return val > 0
}
```

##### vargs
With vargs you can pass a function an unlimited amount of parameters. The parameters will be inside a pseudo array called vargv. The length of the vargs the function receives will be stored inside a variable called vargc.

```
fn combine(string ...) -> string {
    string base
    for(int i=0; i<vargc; i++){
        base = base + vargv[i]
    }
    return base
}
```

#### Importing
Importing from other files is really easy.
```
module math = require("math")
module animationlib = require("./animation.ot")
```

module is a special type of variable. It is impossible to create it without using the require. Module variables will have access to exported functions, variables, and structs by that file. 
