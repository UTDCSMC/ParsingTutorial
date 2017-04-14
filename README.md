# Parsing data line-by-line: A tutorial

> Recognizing the inherent structure in your data, and parsing it in a logical manner solves half of your problems, and prevents the other half from arising.
>
> -- A mentor (i.e. a coding ninja and guru you should listen to)

Algorithmically, it doesn't matter if you are reading input from the console or from a file - they are both *input streams*. We'll assume it is in a file. Some comments along the way should help you do it for console input too.

Let us say your data is entered in rows of

    Fleet_Street 1212, Dallas TX
    Barn_Street 3241, Los_Angelos CA
    Drury_Lane 3591, Muffin MA

# C++ tools for strings

-   C++ style strings are like arrays because they have positional indices.

    ```cpp
    string line = "hello world";
    line[0]; // returns 'h'
    line[1]; // returns 'e'
    line[2]; // returns 'l'
    ...
    ```

-   Unlike arrays, they have functions that you can write after the dot.

-   `string.find(string search)` - Returns the first index where the `search` appears inside the original string. If it doesn't appear anywhere, returns `string::npos` ([See use 1](http://www.cplusplus.com/reference/string/string/find/)).

    ```cpp
    string line = "Drury_Lane 3591, Muffin MA";
    line.find("Lane"); // returns 6
    line.find(" "); // returns 10
    line.find("Blahlah"); // returns string::npos because it is not found
    ```
        
-   `string.find(string search, int start)` - Is the same as `line.find(search_string)`, but it only begins searching after the index `start` ([See use 1](http://www.cplusplus.com/reference/string/string/find/)).

    ```cpp
    string line = "Drury_Lane 3591, Muffin MA";
    line.find(" ", 0); // returns 10, which is the index of the space after "Drury_Lane'
                       // because that is the first space starting from the index 0
    line.find(" ", 10); // returns 10, which is the index of the space after "Drury_Lane"
                        // because that is the first space starting from the index 10
    line.find(" ", 10+1); // returns 16, which is the index of the space after the comma
                          // because that is the first space starting from the index 10+1
    ```

-   `string.substr(int a, int b)` - Returns a substring containing `b` characters, beginning from index `a` ([More](http://www.cplusplus.com/reference/string/string/substr/)).
    ```cpp
    string line = "Drury_Lane 3591, Muffin MA";
    line.substr(4, 11); // returns "y_Lane 3591"
    ```
-   `string.substr(int a)` - Returns the substring of the string beginning from index `a`, all the way to the end.
    ```cpp
    string line = "Drury_Lane 3591, Muffin MA";
    line.substr(4); // "y_Lane 3591, Muffin MA";
    ```

# Basic example

-   A delimiter is something that separates two fields in a line. 
    ```cpp
    string line = "first_part_data, second_part_data";

    // because substr starts at 0 and cuts line.find(", ") characters
    // but line.find(", ") returns the index of the first space
    string first_part = line.substr(0, line.find(", ")); // returns "first_part_data"

    // because line.substr starts at line.find(", ")+2 and takes the rest of the string
    // and line.find(", ") finds the (start) of the comma-space
    // and we want to start after the comma and after the space
    string second_part = line.substr(line.find(", ") + 2); // returns "second_part_data";
    ```

# File example
If the delimiter we are searching for has two characters (eg. ", "), then 

```cpp
  ifstream file ("input.txt");
  string line;
  while (getline(file, line, '\n')) {              // Read an entry. Do getline(cin, entry, '\n') in case of console input.
	string street = line.substr(0, line.find(" ")); // returns "Drury_Lane"

	// shorten line to exclude street
	line = line.substr(line.find(" ") + 1); // returns "3591, Muffin MA";

	string address_string = line.substr(0, line.find(", ")); // returns the string "3591"
    // StringTOInteger is called STOI
	int address = stoi(address_string); // returns the INTEGER 3591
    // On some Windows Systems this won't work. Try
    //int address = atoi(address_string.c_str()); // returns the INTEGER 3591

	// shorten line to exclude address
	line = line.substr(line.find(", ") + 2); // returns "Muffin MA";

	string city = line.substr(0, line.find(" "));

	// shorten line to exclude city
	line = line.substr(line.find(" ") + 1);

	string state = line;
    
	cout << "street=[" << street << "], address=[" << address << "], city=["
		 << city << "], state=[" << state << "]" << endl;
  }
```

# Example with error handling

```cpp
  ifstream file ("input.txt");
  string line;
  int split;
  bool success;
  while (getline(file, line, '\n')) {              // Read an entry. Do getline(cin, entry, '\n') in case of console input.
	split = line.find(" ");
	success = false;
	if (split != string::npos) {
	  string street = line.substr(0, split);
	  line = line.substr(split + 1);

	  split = line.find(", ");
	  if (split != string::npos) {
		string address_string = line.substr(0, split);
		int address = stoi(address_string);
		line = line.substr(line.find(", ") + 2);

		split = line.find(" ");
		if (split != string::npos) {
		  string city = line.substr(0, split);
		  line = line.substr(split + 1);

		  string state = line;
		  cout << "street=[" << street << "], address=[" << address << "], city=["
			   << city << "], state=[" << state << "]" << endl;
		  success = true;
		}
	  }
	}
	if (! success) {
	  cout << "Unable to parse line after \"" << line << "\"" << endl;
	}
  }
```
