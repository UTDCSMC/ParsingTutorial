# Parsing data line-by-line: A tutorial

> Recognizing the inherent structure in your data, and parsing it in a logical manner solves half of your problems, and prevents the other half from arising.
>
> -- A mentor (i.e. a coding ninja and guru you should listen to)

Algorithmically, it doesn't matter if you are reading input from the console or from a file - they are both *input streams*. We'll assume it is in a file. Some comments along the way should help you do it for console input too.

Let us say your data is entered in this manner (it can be in a file, or typed in):

```
<data11> <delim1> <data12> <delim2> ..... <data1n> <delimn /*often '\n' */>
<data21> <delim1> <data22> <delim2> ..... <data2n> <delimn /*often '\n' */>
.
.
.
<datam1> <delim1> <datam2> <delim2> ..... <datamn> <delimn /*often '\n' */>
```

Each line is an *entry*, and each entry has several pieces of *data*, separated by *delimiters*. For example:

```
Joy 20:00   Scottsdale, AZ      # Comment. Delimiters in order: (' ', ':', '\t', ',', '#', '\n')
Ward 21:30  New York City, NY   # Another comment. Not useful for the program.
.
.
.
Danny 23:00 Dallas, TX          # Last comment
```

Here is a robust, battle-tested way of parsing data with such structure. 

## 1.  C++
1. Include:
    - `<iostream>` - For `cin`, `cout` etc.
    - `<fstream>` - For `ifstream`, `ofstream`, `fstream` etc.
    - `<string>` - For using the `string` type.
    - `<cstdlib>` - For using functions like `stoi`, `atoi`.
    
 2. Key functions:
    - `string::substr(a, b)` - Returns a substring containing *b* characters, beginning from index *a* ([More](http://www.cplusplus.com/reference/string/string/substr/)).
        ```cpp
        string p = "Hello";
        cout << p.substr(1,3);
        // Output = "ell"
        ```
    - `string::substr(a)` - Returns the substring of the string beginning from index 'a', all the way to the end.
        ```cpp
        string p = "Hello"
        cout << p.substr(1);
        // Output = "ello"
        ```
    - `string::find(str)` - Returns the index where the string `str` appears inside this string. If it doesn't appear anywhere, returns `string::npos`.
        ```cpp
        string p = "Hello"
        cout << p.find("llo");
        // Output = 2
        cout << p.find("elp");
        // Return value = string::npos. Probably no printable output
        if (p.find("elp") == string::npos) { cout << "yo"; }
        // yo
        ```
        
 3. Open the file:
    ```cpp
    ifstream file("filepath");
    ``` 
    
 4. Read and process the data one line (entry) at a time, in a loop over all entries:
    ```cpp
    string entry;                               // Holds one entry at a time.
    
    // While there are entries to read of the specified structure,
    // delimited by the last delimiter 'delimn', we will iterate.
    // 'delimn' is often the newline character '\n'.
    
    while (getline(file, entry, 'delimn')) {            // Read an entry. Do getline(cin, entry, 'delimn') in case of console input.
        string rest = entry;                            // As we read data, we'll cut the entry short and remove what we have already read from it. 
        // Initially rest = entry because we haven't read anything.
        int read_from = 0;                              // We start reading the current piece of data from this index;
        string data1 = rest.substr(read_from,           // The substring of entry beginning at read_from
            rest.find("delim1")                         // And ending at the index of the first delimiter.
        );                              
        if (/* needed data type for data1 is int (or float) */) {
            int data1Int = stoi(data1);                 // stof(data1) in case of float.
            // if this doesn't work, try:
            // int data1Int = atoi(data1.c_str());
            // (atof in case of float).
        }
        // Do whatever with data1.
        read_from = entry.find("delim1") + 1;           // The next time we have to read something, it will be after delimiter 1.
        string data2 = rest.substr(read_from,           // The substring of entry beginning at read_from
            rest.find("delim2")                         // And ending at the index of the second delimiter.
        );
        if (/* needed data type for data2 is int (or float) */) {
            int data2Int = stoi(data2);                 // stof(data2) in case of float.
            // if this doesn't work, try:
            // int data2Int = atoi(data2.c_str());
            // (atof in case of float).
        }
        // Do whatever with data2.
        read_from = entry.find("delim2") + 1;           // The next time we have to read something, it will be after delimiter 1.
        //.
        //.
        //. Parse all datai, from data1 through data_n, in this way.
        //.
        //.
        // Do whatever with data_n. If it's a comment, ignore it.
    }
    ```

See the pattern? Do it this way and we guarantee your project will go much smoother.
