# **Simple instructions to begin using the simple ini parser** #



## Abstract ##
This page will show code examples that will help you use this parser for reading the contents of an INI file.

## INI File structure ##
An INI file const of various sections, each of one defines various unique keys with an unique value assigned to each key.

Sections are declared as a unique word enclosed in square brackets. Spaces enclosed into the square bracktes are ignored, but the section must be defined with an unique word( [sampleSection ](.md) )

Inside a section, we can define keys with a unique value. Keys must be unique within the same section, but can have the same key name in differents sections. A key is assigned using the equal sign (key = value)

In addition we can define single-line comments using a semicolon ( ; )

So, a simple INI file could be writen like this:
```
[ section ]
;comment
key = value
```


## Details ##
First, we must create an instance of a file INI parser
```
//Create an instance of a ini file parser
IniParser.FileIniDataParser parser = new FileIniDataParser();
```

Now we can access the INI Data using the Method `LoadFile` from the parser object. This returns a new `IniData` instance which contains all the data parsed from the INI file:
```
//Load the INI file which also parses the INI data
IniData parsedData = parser.LoadFile("TestIniFile.ini");
```

All Sections inside the ini file are represented by a `SectionDataCollection`, which is nothing more than a collection of `SectionData` instances.
Each `SectionData` holds a string with the section's name, a list of strings holding the comments associated to that section, and a list of keys in the section. That list is represented by a `KeyDataCollection`, a collection of `KeyData` instances.

### Accessing the data ###
The DataCollection types implements `IEnumerator<T>` so data can be iterated with a `for each` loop:

```
//Iterate through all the sections
foreach (SectionData section in data.Sections)
{
   Console.WriteLine("[" + section.Name + "]");
   
   //Iterate through all the keys in the current section
   //printing the values
   foreach(KeyData key in section.Keys)
      Console.WriteLine(key.Name + " = " + key.Value);
}
```

### Direct access ###
Also, we can access the data directly if we know the name of the section and key we need

```
//This line gets the SectionData from the section "GeneralConfiguration"
KeyDataCollection keyCol = data["GeneralConfiguration"];

//This line gets the KeyData form the key "setUpdate" defined in the section "GeneralConfiguration"
string directValue = keyCol["setUpdate"];

//But is easier to get the value in one shot:
directValue = data["GeneralConfiguration"]["setUpdate"];
```

The parsed data can also be mofified that way:
```
data["GeneralConfiguration"]["setUpdate"] = "newValue";
```

### Adding or removing sections or keys in the sections ###
You can programaticaly add or remove both sections and key/value pairs of the sections

```
//Make a copy of the parsed data
IniData newData = parser.ParsedData;

//Add a new section and some keys
newData.Sections.AddSection("newSection");
newData["newSection"].AddKey("newKey1", "value1");
newData["newSection"].AddKey("newKey2", "value5");

//Remove the last key
newData["newSection"].RemoveKey("newKey2");

//Remove the 'Users' section from the file and all keys and comments associated to it
newData.Sections.RemoveSection("Users");
```

### Saving the file ###
Either if you make modifications to the parsed data, or if you create a new `IniData` instance and populate it, you probably want the data to be saved to a file. To do that, just use the 'SaveFile' method from the `FileIniDataParser` instance:

```
//Get the data
IniData parsedINIDataToBeSaved;
//Save the file
parser.SaveFile("newINIfile.ini", parsedINIDataToBeSaved);
```

## Relaxed parsing and global section ##
As INI file format is not an standard and implementations varies, an special parsing methods is implemented. Just call any parsing method using the two parameter overload, where the second parameter is a boolean. If that parameter is set to true the following rules are applied:
**INI files with key/value pairs with no section will be stored in the "global" special section:
```
/*
special_ini_file.ini contents:
myKey = I'm in the global section!

[Section1]
myKey2 = value2
*/
IniData parsedData = parser.LoadFile("special_ini_file.ini");
var myValue = parsedData.Global["myKey"]; // myValue contains string "I'm in the global section!"
```**

**The Ini file can have sections with the same name. All key value pairs will be in the proper section. If the relaxed parsing is not used in an INI file with two ore more sections with the same name, an exception will be thrown.**

## Advance usages ##
You can change the characters used as delimiters in the ini file:
```
// Parsers inherit from StreamIniDataParser, so this properties
// are available for all parsers
StreamIniDataParser parser = new StreamIniDataParser();

// Change character used as comment delimiter (defaults to ';')
parser.CommentDelimiter = '#';

// Change character used as key-value separator (defaults to '=')
parser.KeyValueDelimiter = '-';

// Change characters used as section delimiters (defaults to '[' and ']')
parser.SectionDelimiters = new char[] { '<', '>' };
```


---


## Contents of the INI file used in the example ##
The INI file used in the example contains the following info:
```
;This section provides the general configuration of the application
[GeneralConfiguration] 

;Update rate in msecs
setUpdate = 100

;Maximun errors before quit
setMaxErrors = 2

;Users allowed to access the system
;format: user = pass
[Users]
ricky = rickypass
patty = pattypass
```