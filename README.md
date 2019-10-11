

# Protocol Buffers

### What are protocol buffers?
In short, Protocol Buffers allow us to write `messages` in order to send structured data to programs written in different languages. We can define our structure *once* in a `.proto` file and use this to generate the necessary classes in the respective languages. This is useful for integrating software written different languages and can serve as a replacement for `JSON`, `XML` or other data-formats that allow us to send information between different programs.

## Protocol Buffer Messages

## 
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



Now, let's say that this definition of a square isn't sufficient and that we need more information, such as the color of the square. We can simply extend the message!

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

Now we have sufficient information to say where a given square is located, as well as define it's RGB color. Note that Protocol Buffers are backwards-compatible. If you add a new field to an existing Protocol Buffer Message, programs that read the old structure will simply ignore the new fields. More information about this and the rules you need to follow can be found [here](https://developers.google.com/protocol-buffers/docs/javatutorial#extending-a-protocol-buffer).

### Repeated Message Fields
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

### Writing Messages

Protocol Buffer Messages alone are useless, we need some way to use these messages in our application and some way to write them to a `file` or `stream` so that they can be shared. This is where the `Protocol Buffer Compiler` comes in.

There are bindings for the Protocol Buffer Compiler for multiple languages, such as Python, Java, Scala, Go, C++, C# and Dart. This allows you to specify the location of your `.proto` file and which language you want to output classes for. Here's an example

`protoc -I=. --python_out=. board.proto`

This example assumes the `board.proto` file is present in the current directory, then takes this file and outputs classes in the current directory that will allow you to read and write Protocol Buffer Messages. More information on the generated classes can be found [here](https://developers.google.com/protocol-buffers/docs/reference/java-generated).

Now let's look at an example of writing a protocol buffer message in Python and writing the result to a file. We'll continue to use our example of Squares. Once you run the `protoc` command to generate the necessary classes you should have a file named `board_pb2.py`

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

Note: Protocol Buffers are neat in the sense that you can print their values directly in several languages, this is useful if you want to verify that you've assigned the correct information before serializing the messages.

### Reading Messages in a different language
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NTkzMDgwNywtNjk2NzY1MzM1XX0=
-->