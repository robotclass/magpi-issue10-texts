Introducing streams
===================

Difficulty: MEDIUM

Alex Kerr, Guest Writer


We've covered some of the basics now, and you
may be starting to notice some similarities
between C++ and C. From this issue onwards,
we will be covering what makes C++ and C
different. For the basics like if statements and
loops, have a read of The C Cave.


Compatibility with C
--------------------
Several of the standard C library header files are
accessible from C++. To use them, include them
as you would any other header, but add a 'c' to
the front of the name and take away the '.h'. For
example, the C code:

    #include <stdlib. h>
    #include <stdio. h>

Becomes the following in C++:

    #include <cstdlib>
    #include <cstdio>

This will give you access to all the functions
inside these libraries, such as printf(), and
rand(), which allows C++ to borrow a lot of useful
features from C.


I/O Streams
------------
Not everything is the same though, as you will
have noticed. For example, when we were
outputting, we used cout instead of printf(). This
is because C++ uses I/O (short for Input/Output)
streams.

What this really means is that the things that
control input and output are just fancy objects,
instead of functions. Objects and classes will be
covered in depth later on, as that is the
fundamental difference between C and C++.

There are three main I/O stream headers. These
are <iostream>, which controls output and input
to and from the console, <fstream> which is used
for files, and <sstream>, which is used for
strings.

We already know how <iostream> works, so let
us look at <fstream>.


Reading and Writing Files
-------------------------
    #include <iostream>
    #include <fstream>
    #include <string>
    using namespace std;
    int main()
    {
    // Create an input file stream named 'reader':
    ifstream reader(“test.txt”);
    while(reader.good())
    {
        string temp;
        getline(reader, temp);
        cout << temp << endl;
    }
    reader.close();
    return 0;
    }

That code allows you to read in a file named
'test.txt' (in the same directory as the executable)
and output its contents. You could use the '>>'
symbol, as we do with cin, but you will find it only
reads up to a space.

You get this issue with cin as well, so if you're
trying to get something like a name which needs
spaces, you could use:

  getline(cin, variable);

And that will read the whole input and save it to
'variable', which needs to be a string.

For outputting, we can use it just like cout:

  #include <iostream>
  #include <fstream>
  #include <string>
  using namespace std;
  int main()
  {
    // Creates an output fi le stream named ' writer' :
    ofstream writer(“test. txt”);
    // Write some text to the fi le:
    writer << “Hello” << endl;
    writer << “This is a test fi le” << endl;
    writer. close();
    return 0;
  }

This program creates a file named 'test.txt' – in
the same directory as the executable – with the
text you see. However, this will overwrite any
files already there, so be careful!

To avoid this happening, and to do some other
interesting things, there are various options we
can use when making our file stream objects:

    +-------------+------------------------------+----------------------------+
    |   Option    |         Description          |           Notes            |
    +-------------+------------------------------+----------------------------+
    | ios::app    | Appends to an existing file, |                            |
    |             | instead of overwriting it.   |                            |
    | ios::in     | Gets input from a file.      | Default when used          |
    |             |                              | with ifstream(filename)    |
    | ios::out    | Outputs to a file            | Default when used          |
    |             |                              | with ofstream(filename)    |
    | ios::binary | Reads the file in as binary, |                            |
    |             | instead of text.             |                            |
    +-------------+------------------------------+----------------------------+

These can be added as options when you first make the stream. For example:

    // Makes a stream that can input and output.
    fstream(filename, ios::in, ios::out);
    // Makes a stream that appends to a file:
    ofstream(filename, ios::app);

When a stream is created is it associated with a
memory buffer. When information is written into
the stream or read from the stream, information
is read from or written to the memory. This
means that if an output file is used, but the file is
not closed some of the data may not be written to
the output file. When a file is closed, the any
information in the associated buffer is flushed to
the file. While the fstream close function does
flush the stream, some streams may require
explicit flushing.

C++ streams are said to be "type safe", since
they can be used to read an input value into a
type without the need to necessarily use that
type. In C, this is not the case and sscanf
functions require projection.

Next time we will look at strings and <sstream>,
allowing you to convert strings to different data
types. Hopefully you can begin to see how all the
streams are similar, but suited to their job. Try
inputting and outputting from files, and see what
interesting things you could do.
