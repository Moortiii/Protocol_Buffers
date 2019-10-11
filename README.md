

# Protocol Buffers

### What are protocol buffers?
In short, Protocol Buffers allow us to write `messages` in order to send structured data to programs written in different languages. We can define our structure *once* in a `.proto` file and use this to generate the necessary classes in the respective languages. This is useful for integrating software written different languages and can serve as a replacement for `JSON`, `XML` or other data-formats that allow us to send information between different programs.

## Creating Protocol Buffer Messages

### Writing a message
Protocol Buffer Messages are structured blocks of data. The rules of messages are relatively straightforward. Messages are defined in blocks prefixed with the keyword `Message` followed by the name of the message, note that this name is case-sensitive.

Variables are defined using a `variable type`, a `name` and an `identifier`.

````java #use java to get some syntax highlighting
syntax="proto3";

// int32 = variable type
// x     = name
// 1     = identifier

message Square {
    int32 x = 1;
    int32 y = 2;
}
````

`Identifiers` have to be unique for their messages and are used for the encoding and decoding of the resulting serialized output. The next field added to this message would have the `identifier` of `3`.

### Extending a message

Now, let's say that it turns out that this definition of a square isn't sufficient for our needs and that we need more information. Perhaps we need to include the color of the square as well? In that case we can simply extend the message.

````java #use java to get some syntax highlighting
// Filename: board.proto

syntax="proto3";

message Square {
    int32 x = 1;
    int32 y = 2;
    int32 green_value = 3;
    int32 red_value   = 4;
    int34 blue_value  = 5;
}
````

Protocol Buffers are backwards-compatible. If you add a new field to an existing Protocol Buffer Message, programs that read the old structure will simply ignore the new fields. More information about this and the rules you need to follow can be found [here](https://developers.google.com/protocol-buffers/docs/javatutorial#extending-a-protocol-buffer).

### Sending lists of data
A lot of information can be structured using simple data types such as integers and booleans, but this is not always sufficient. When using Protocol Buffers, you will quickly see the need for a way to send `arrays` or `lists` of information. Luckily, Protocol Buffers have built-in support for this using the `repeated` keyword. Let's look at an example.

````java
// Filename: board.proto

syntax="proto3";

message Board {
    message Square {
        int32 x = 1;
        int32 y = 2;
        int32 green_value = 3;
        int32 red_value   = 4;
        int32 blue_value  = 5;
    }
    
    repeated Square squares = 1;
}
````

Now we can create messages with `Boards` allowing us to send multiple `Squares` at once!

## Using Protocol Buffer Messages

### Generating the necessary classes

We can create instances of our Protocol Buffer Messages and write them to a `file` or `stream`, this allows us to share them with other applications. In order to do so, we need to generate language-specific classes for our `.proto` file. The protocol buffer compiler, `protoc` allows us to create classes for the following languages:

1. Python
2. Java
3. Go
4. C++
5. C#
6. Dart

Assume we have the following file structure:
````
project/
    - board.proto
    - main.py
````

We can then type the following command to generate a `board_pb2.py` file in the current directory using `protoc`:

`protoc -I=. --python_out=. board.proto`

More information on the generation can be found [here](https://developers.google.com/protocol-buffers/docs/reference/java-generated). It is also possible to specify a path to the `.proto` file using the `--proto_path=` argument.

### Creating instances of Protocol Buffer Messages in Python

Now let's look at an example of creating an instance of a protocol buffer message in Python and writing the result of it to a file. The example below generates two squares, one red and one blue.

````python
// Filename: main.py

import board_pb2

board = board_pb.2.Board()

# Create a completely red square at (1, 1)
square_buffer_1 = board.squares.add()

square_buffer_1.x = 1
square_buffer_1.y = 1
square_buffer_1.red_value   = 255
square_buffer_1.green_value = 0
square_buffer_1.blue_value  = 0

# Create a completely blue square at (4, 5)
square_buffer_2 = board.squares.add()

square_buffer_2.x = 4
square_buffer_2.y = 5
square_buffer_2.red_value   = 0
square_buffer_2.green_value = 0
square_buffer_2.blue_value  = 255

# Write the result to a file
with open("output.bin", "wb") as f:
    f.write(board.SerializeToString())
````

*Note*: Protocol Buffers are neat in the sense that you can print their values directly in several languages, this is useful if you want to verify that you've assigned the correct information before serializing the messages.

### Reading Protocol Buffer Messages in a different language

Now that we have generated a binary file we can parse it again using a different language. For this example we will use Scala and need to download and install the `ScalaPBC` plugin first.

1. ensure an up-to-date version of Java is installed as this is required by `ScalaPBC`.

```
sudo apt install openjdk-11-jre-headless
sudo apt install openjdk-11-jre
sudo apt install openjdk-11-jdk
```

2. Download the current version of `ScalaPBC` from the [following link](https://github.com/scalapb/ScalaPB/releases) and extract it to a directory.

3. Generate the scala classes using the protoc compiler and the `--plugin` flag.

	`protoc neighbors.proto --plugin=./protoc-gen-scala --scala_out=.`
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU2NTk3NTc4MywtNjk2NzY1MzM1XX0=
-->