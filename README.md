# Aldwych
JSON parser/creator for Swift &ndash; for parsing, editing, creating and reconstituting of parsed data. Note: Master branch is Swift 1.2 compatible and Swift 2 branch is Swift 2 compatible (and currently being tested). Following the addition of recursive enums to Swift, [Aldwych 2](https://github.com/sketchytech/Aldwych-2-JSON-Parser-for-Swift) was created. This is now the most current code and most frequently maintained. It is available in the form of a Swift Playground with examples included within it.


#Guiding Principles
The guiding principles of the parser are:

* it should be able to take any valid JSON dictionary or array
* it will enable the structure of the data to be interrogated without any foreknowledge of the values and keys contained
* JSON should remain editable and be able to be reconstituted easily
* internally the types used to manipulate the JSON are type safe and do not store AnyObject values
* the programmer implementing the parser can choose (and easily alter) the interpretation of type safety used for values
* JSON can not only be parsed but also created from scratch in a type safe manner
* structure of JSON can be further refined through adding API specific types. This can reduce the size of manipulated data for use in UITableView, etc., while still making it possible to edit and reconstitute.

## Supporters
![Grumpy Goat Interactive][logo]

[Grumpy Goat Interactive](http://www.grumpygoat.ca)

![Gylphi Limited][logo2]

[Gylphi Limited](http://www.gylphi.co.uk)
[logo]: https://github.com/sketchytech/Aldwych_JSON_Swift/blob/master/readme_images/grumpygoat_400.png  "Grumpy Goat Interactive"
[logo2]: https://github.com/sketchytech/Aldwych_JSON_Swift/blob/master/readme_images/gylphi_logo_twitter.jpg  "Gylphi Limited"
## Endorsements
"Aldwych played a pivotal role in deploying an update to my client's app. Being able to manipulate the JSON object was a huge time saver, and made the typical cumbersome approach a very pleasant experience." [Mat Cartmill](http://twitter.com/MatCartmill), **Grumpy Goat Interactive**

*If you've used Aldwych in an app then it would be great to feature your experience on this page and a link to your app. Contact: [@sketchyTech](http://twitter.com/sketchyTech).*
#Naming
This repository ~~follows the logic of many other JSON repositories in that the name is derived from some connection to Jason, the Greek mythological hero. Iolcos was Jason's starting point in the myth of the Golden Fleece, and this JSON parser is a new starting point for the parsing of JSON in Swift.~~ is randomly named after a London street. Really it makes no sense, because Aldwych is a one-way street and this parser is the opposite. It's all about working in two directions, but hey it sounded punchier than iolcos. (Note: a part of me wanted to call it Vodka &ndash; SwiftSummit 2015 attendees will understand this one.) 
#Capabilities and Requirements
The parser is capable of handling JSON that has been loaded as NSData and which follows an array or string : value dictionary format. (In other words standard JSON.) It is also capable of creating JSON data from scratch.

The code requires the latest Xcode 6.3 Beta installed with Swift 1.2
#How to Use
To use simply copy the contents of the Sources folder into your app or playground. It works with iOS and OS X.
##Parse JSON Data
To parse NSData simply use

```swift
JSONParser.parseDictionary(json:NSData, restrictTypeChanges:Bool = true, anyValueIsNullable:Bool = true) -> JSONDictionary?`
```
or 

```swift
JSONParser.parseArray(json:NSData, restrictTypeChanges:Bool = true, anyValueIsNullable:Bool = true)  -> JSONArray?
```

Default settings do not allow changes of type but do allow values to become null. To enable changes of type change **restrictTypeChanges** to **false** and to prevent nullability of values change **anyValueIsNullable** to **false**.

These properties can be changed at any time, and of course if the JSON is parsed into a constant value then no changes will be possible anyway until the dictionary or array is copied to a variable.
##Retrieve and update values
In order to retrieve a value identify its type, e.g. `if let tName = jsonDictionary["trackName"]?.str {}` or `if let rCount = jsonDictionary["resultCount"]?.num {}`

Updates to results can be made like this `jsonDictionary["results"]?[0]?["trackName"] = "Something"`
#Creation of JSON
```swift    
var myJSON = JSONDictionary()
// A JSONDictionary instance has been created
myJSON["Third"] = "One"
// A string key with string value has been added
myJSON["Fourth"] = myJSON
// A copy of the JSONDictionary has been added creating a nest
myJSON["Fourth"]?["Third"] = "Two"
// a nested value is changed
if let aStr = myJSON["Fourth"]?["Third"]?.str {
    println(aStr) // Two
}
// A value is extracted from the nested JSONDictionary
myJSON.jsonData()?.writeToFile("/tmp/myJSON.json", atomically: false)
// JSON has now been written to /tmp directory
```
Once the JSON has been created in this way, the file you created will contain text that looks like this:

`{"Fourth":{"Third":"Two"},"Third":"One"}`

Or similar (remember dictionaries are not ordered in the way that arrays are).
##Initialize with Dictionary<String,T>
It's also possible to initialize the JSONDictionary using a Swift Dictionary, e.g.

    let myDictionary = ["First":9,"Second":1,"Third":"Two"]
    var myJSON = JSONDictionary(dict:myDictionary)
    myJSON.jsonData()?.writeToFile("/tmp/myJSON.json", atomically: false)
    // JSON has now been written to /tmp directory

The created JSON:

`{"First":9,"Second":1,"Third":"Two"}`

Creating JSON using **Aldwych** means that you can take advantage of all the associated methods and properties while forgetting about AnyObject and casting. It is also type safe.
#Type Safety
By default we cannot change type once it is set. For example:
    
    var myJSON = JSONDictionary()
    // A JSONDictionary instance has been created
    myJSON["Third"] = "One" // "One"
    myJSON["Third"] = 1 // "One"
    myJSON["Third"] = "Two" // "Two"

But if we were to do this:
    
    var myJSON = JSONDictionary(restrictTypeChanges: false)
    // A JSONDictionary instance has been created
    myJSON["Third"] = "One" // "One"
    myJSON["Third"] = 1 // 1
    myJSON["Third"] = "Two" // "Two"

Then type changes would be freely permitted. But this behaviour changes again if the `restrictTypeChanges` property is later set back to true:

    var myJSON = JSONDictionary(restrictTypeChanges: false)
    // A JSONDictionary instance has been created
    myJSON["Third"] = "One" // "One"
    myJSON["Third"] = 1 // 1
    myJSON.restrictTypeChanges = true
    myJSON["Third"] = "Two" // 1

By default these settings and changes are inherited by nested JSONDictionary and JSONArray types, but this can be changed by setting the `inheritRestrictions` to false on the top level object.
#Methods and Properties
There are a range of methods available that will expand with time. They include among others (to be documented soon):
##JSONArray
`jsonArray.append("Hello Swift!") // can be used for strings, numbers, JSONArray, JSONDictiony, NSNull`

`jsonArray.insert("Hello Swift!",atIndex:2) // can be used for strings, numbers, JSONArray, JSONDictiony, NSNull`

`jsonArray.isStringArray() -> Bool // i.e. is exclusively composed of strings, nothing else`

`jsonArray.containsStrings() -> Bool // has strings within array`

`jsonArray.stringArray() -> [String]? // returns only the strings from an array`
##JSONDictionary
`jsonDict.keys: [String]? {get}`

`jsonDict.keysWithStringValues: [String]? {get}`

`jsonDict.keysWithNumberValues: [String]? {get}`

`jsonDict.keysWithNullValues: [String]? {get}`

`jsonDict.keysWithArrayValues: [String]? {get}`

`jsonDict.keysWithDictionaryValues: [String]? {get}`

#Loops

        for (k,v) in jsonDictionary {
            if let aNum = v?.num {
              println(aNum)
            }
        }

and

        for v in jsonArray {
            if let aNum = v?.num {
              println(aNum)
            }
        }


#Bool handling (recently added)

```
if let fileURL = NSBundle.mainBundle().URLForResource("my", withExtension: "json"),
            data = NSData(contentsOfURL: fileURL),
            json = JSONParser.parseDictionary(data, handleBoolValues: true) {
                
                if let b = json["this"]?.bool {
                    println(b)
                }
                
                if let n = json["that"]?.num {
                    println(n)
                }
                println(json.keysWithBoolValues)
                println(json.stringify(options: nil, error: nil))
                
        }
```
#Round-tripping data
To round-trip the JSON simply write

`myJSON.jsonData() -> NSData?`

it works for JSONDictionary and JSONArray

#Refined Handling
For more refined handling of data, additional types can be created. If you add the iTunesData file from the Extras folder then code like this can be can be written:

    var data:NSData?
    if let url = NSURL(string:"https://itunes.apple.com/search?term=b12&limit=10"),
        d = NSData(contentsOfURL: url) {
          data = d
    }
    if let data = data,
        parsedJSON = JSONParser.parseDictionary(data),
        iTD = iTunesData(dict: parsedJSON) {
        var tracks = map(iTD.results, {x in Track(dict:x.jsonDict)})
        let m = map(tracks,{x in "Title: \(x!.trackName), Album: \(x!.collectionName)\n"})
        println(join("\n",m))
        tracks[1]?.trackName = "New Name"
        tracks[1]?.trackName
        var iT = iTD
        if let track = tracks[1] {
            iT.updateTrackDetails(track)
        }
        tracks = map(iT.results, {x in Track(dict:x.jsonDict)})
        let m2 = map(tracks,{x in "Title: \(x!.trackName), Album: \(x!.collectionName)\n"})
        println(join("\n",m2))
        iT.outputJSON()?.writeToFile("/tmp/b12.json", atomically: false)
    }
It's an example of how the data can be filtered but still renconstituted. Note: an explanation of the logic and also suitable patterns for building new types for other APIs is planned. 

## Just added
### Regular Expressions (GREP) for String Values
The `replaceStringUsingRegularExpressionInJSONDictionary()` and `replaceStringUsingRegularExpressionInJSONArray()` functions make RegEx changes to all strings in the JSONArray or JSONDictionary (including any nested strings). For example:
```
        var myError:NSError?
        var dict = JSONDictionary(dict: ["a":["I was reading the paper"],"b":"while sitting on a chair"])
        replaceStringUsingRegularExpressionInJSONDictionary("paper|chair", &dict, withString: "pineapple")
        println(dict.stringify(options: nil, error: &myError)) // {"b":"while sitting on a pineapple","a":["I was reading the pineapple"]}
```
Note: key values are not changed by RegEx using this function.
### Further Early Replace Functions
A series of replace functions have been added for strings, dictionaries and arrays. These are designed to work with parsed XML (see below). They work through the entire supplied dictionary and search all values that are arrays for nested dictionarys.
```
if let d = jsonData,
          j = JSONParser.parseDictionary(d)
           {
            var json = j
            replaceValue("li", &json, string:"List item replaced")
            println(json.dictionary)
            }

```
Where the value is nested inside a dictionary that is the value of a parent dictionary it currently fails (it must currently be an array of dictionaries for the search to proceed). As the heading states this is early functionality, it works well in a scenario like the following: XML has been parsed by Aldwych which contains list items `<li>`. It doesn't matter how deeply nested within `<div>`s and so on, all values with be replaced with the specified string, array, dictionary, number, etc. (Note: There's also a replaceValueWithNull() function.)

Further refinement and improvement will be added for more general cases.
## Recently added
### XML to JSON Parsing
```
var error:NSError?
if let url = NSBundle.mainBundle().pathForResource("test", ofType: "xml"),
   d = NSData(contentsOfFile: url)
   {
        let a = XMLParser()
        let jsonData = a.parse(d).jsonData(options: nil, error: &error)
   }
```
###JSON to XML Parsing
Going back the other way:
```
if let json = JSONParser.parseDictionary(data),
   str = XMLParser.json2xml(json) {
    // if the json was of a type that could be converted to XML, i.e. it follows the same structure as XML output by the parse() method of XMLParser, then a valid XML string will be returned, else nil will be returned
   }
```
Read more about [XML to JSON parsing in Aldwych](http://sketchytech.blogspot.com).

Also added a stringify() method to JSONArray and JSONDictionary.
#Future Functionality
The aim is to enable the JSONDictionary and JSONArray types to mirror the functionality of Dictionary and Array and to have them adhere to the same protocols. Some of this is already in place and the exact methods and properties that are accessible will be listed here soon.

At present insert and append methods do not restrict type at all. It will be considered how type safety might be applied here so as to restrict the corruption of JSON structure and data. However, if this is a concern then it may well be the place of refined handling types to ensure illegal changes are not made to data.
## In preparation
* ~~Two-way bool handling (currently bool values are imported and accessed as numbers: 1 and 0)~~ **added 7 May 2015**
* Markdown parsing to JSON (exportable to XML)
* [DOCX](http://blogs.msdn.com/b/chrisrae/archive/2010/09/25/where-is-the-documentation-for-office-2010-s-docx-xlsx-pptx-formats.aspx) to JSON (exportable to XML)
* XML to EPUB &ndash; as part of the [json-book](https://github.com/sketchytech/json-book)
* translation to and from NSAttributedString for display of text

#Contact
You can get in touch on twitter [@sketchyTech](http://twitter.com/sketchyTech).
