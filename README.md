# Understand-The-URL
This Tutorial discuss the important information about the URL in Android System. 

### This Tutorial discusses the following:
1. What is the URL ?
2. Construction Sections of the URL
3. The difference between using "path", "pathPrefix", and "pathPattern" attributes
4. What is the mimeType attribute ?
5. Important Notes to be considered
6. How to customize the Android App Links as per your needs.

### This is a part of Android App Links Series.

# What is the URL 
Before we start using the Android App Links, We should know some important information about the URL and how it is constructed. 

As per Wikipedia "URL is a Uniform Resource Locator (URL), which is a reference to a web resource that specifies its location on a computer network and a mechanism for retrieving it, for example, https://www.example.com:3000/index.html"
https://en.wikipedia.org/wiki/URL

In Other Words, URL is a well constructed string which points to the location of the data that needs to be retrieved. It follows a certain format technique to construct the final URL string.

# The URL consists of 4 parts
1. Scheme 
2. Host 
3. Port 
4. Path 

       <scheme>://<host>:<port>/<path>
  
#### If we get back to the sample URL provided earlier "https://www.example.com:3000/index.html", We will find that
1. Scheme = https
2. Host = www.example.com
3. Port = 3000
4. Path = index.html

The Andoid System has make use of this URL format and extend its functionality to be used in the Android App Links feature. It actually gives you more control on the URL that could access your app also the power to filter out the URL using the data type sent along with it. This is done by using the <data> tag

#### So, from the Android System Point Of View, Here is the <data> tag construction parts which get interpreted to a URL string
1. Scheme
2. Host
3. Port
4. Path / PathPrefix / PathPattern
5. mimeType

    <data android:scheme="string"
      android:host="string"
      android:port="string"
      android:path="string"
      android:pathPattern="string"
      android:pathPrefix="string"
      android:mimeType="string" />
      
#### This get interpreted to

    <scheme>://<host>:<port>[<path>|<pathPrefix>|<pathPattern>]

https://developer.android.com/guide/topics/manifest/data-element.html#path

Here as you can see, for more flexibility, You don't have to provide the exact path but you could also just specify the start text of your path using pathPrefix or you could even provide a pattern representing the expected path using pathPattern.

# What is the difference between using "path", "pathPrefix", and "pathPattern" attributes ?

To explain the difference between using the various path attributes, We will experiment the result of constructed data tag on the following URLs for each attribute
  
https://www.example.com:3000/user

https://www.example.com:3000/userone

https://www.example.com:3000/user_one

https://www.example.com:3000/user/one

#### If we used the "path" attribute, as following:

      <data android:scheme="https"
            android:host="www.example.com"
            android:port="3000"
            android:path="user"/>
        
The result URL will match only with
https://www.example.com:3000/user

It will fail with the rest of the URLs, As the path attribute instructs the android system to exact match the complete URL with the exact resulted URL from the <data> tag

#### If we used the "pathPrefix" attribute, as following:

      <data android:scheme="https"
            android:host="www.example.com"
            android:port="3000"
            android:pathPrefix="user"/>
    
The result URL will match all of the following URLs
  
https://www.example.com:3000/user

https://www.example.com:3000/userone

https://www.example.com:3000/user_one

https://www.example.com:3000/user/one

As the pathPrefix attribute instructs the android system to exact match the URL till the port section and only the first 4 letters from the path section (as "user" consists of 4 letters) and ignore any other text that could follow these letters.

### What about the "pathPattern" attribute ?
pathPattern attribute allows you to use only 3 regex characters which are " . " " * " " \\ ", You could combine them together

#### The " . " dot
- The " . " represents only one character which could be a letter, a number, a symbol and the characters count can't be zero or 2. It should be exactly 1 character.
- If you use " .. " - two dots - this means any two characters and the characters count can't be 1 or 3. It should be exactly 2 characters.
- If you use " ... " - three dots - this means any three characters and the characters count can't be 1, 2  or 4.. It should be exactly 3 characters.

So, the " . " dot instructs the android system that it could be replaced by any character but it restricts the number of characters allowed. Number of characters must equal to the number of dots used.

#### The " * " asterisk
- It can't be used alone.
- It have to be combined with any character or " . " 
- The " * " asterisk only affects the number of allowed characters NOT the type of character, in other words, if we used " e* ", This means any number of the character " e " is allowed. So, it could be " e " or " ee " or " eee " and so on. BUT it can't be " a " nor " ea "
- When combine the dot with the asterisk like this " .* " , This means any character with any count. So, it could be " e " or " ee " or " eee " and so on. Also it could be " a " or " ea ". Actually, it could be " jhavcvsvjs " :)
- When combine more than one dot with the asterisk like this " ..* " , This means any characters with count more than 1. So, The allowed count will be the number of dots minus 1. 

So We could say
- " .* "     = Any Characters with Any Count
- " ..* "    = Any Characters with Any Count more than 1
- " ...* "   = Any Characters with Any Count more than 2
- " ....* "  = Any Characters with Any Count more than 3
- And So On

As the pathPattern attribute instructs the android system to exact match the URL till the port section and matches the path section with the provided path pattern.

#### The " \ " backslash
The Android Documentation said that "The " \ " backslash is used as an escape character". But What does it actually do ?
The " \ " backslash symbol makes the " * " lose its wildcard meaning and become just a normal asterisk character

For Example, In some cases, you may want to use an asterisk " * " as a normal asterisk symbol NOT as being described above. For Example, You want to allow the following URL https://www.example.com/userid*3. If you used the pathPattern like this 

            android:pathPattern="/userid.*"

It will allow all the following URLs
 
 https://www.example.com/userid.3
 
 https://www.example.com/userid-3
 
 https://www.example.com/userid&3
 
 https://www.example.com/userid=3
 
 And So On
 
 But You Want to use the " * " as a simple asterisk character. In This Case, You have to escape the asterisk from its wildcard meaning by using the backslash " \ "
 
 So, The Pattern should be as following
 
            android:pathPattern="/user\*.*"

But What if you want to use a backslash " \ " just as a normal backslash :) . All What you have to do is to escape the backslash with another backslash like this " \\ " .

# What about the mimeType attribute ?
First, What is the mimeType ?

As per Mozilla "A media type (also known as a Multipurpose Internet Mail Extensions or MIME type) is a standard that indicates the nature and format of a document, file, or assortment of bytes. It is defined and standardized in IETF's RFC 6838."
https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types

So, The mimeType attribute defines the type of the media that will be included with the intent. The mimeType attribute consists of two parts separated by a forward-slash " / ". The first part is the type and the second part is the subtype

       <type>/<subtype>

The subtype can be the asterisk wildcard (*) to indicate that any subtype matches. For example, if you indicate the mimetype attribute as following

       image/*
       
This means any type of image is allowed such as jpeg, png, gif, ... etc 

Also, You can't see the mimeType part in the constructed URL but it still send along with the URL from the application calling your app through an intent. This attribute could be neglected when using Android App Links. It will be used in case of an app is communicating with your app through startActivity() method.



# Very Important Notes to be considered
- The Host section will be meaningless if you didn't specify a Scheme
- The Port section will be meaningless if you didn't specify a Scheme
- The Path section will be meaningless if you didn't specify a Scheme
- The Path section will be meaningless if you didn't specify a Host
- If you didn't specify a Port, This means you can't use any URL which contains any post even if all other conditions are matched.
- You could use the asterisk " * " in the first part of the host like this "*.google.com". This will allow the following URLS
"developer.google.com" & "www.google.com" & ".google.com" & so on...
- The asterisk  " * " must be the first character of the host attribute. For example, the host "google.co.*" is invalid because the asterisk wildcard is not the first character.
- It's common for an intent filter to declare a \<data> tag that includes only the android:mimeType attribute.
- android:mimeType attribute will set the default scheme to content: and file: if you didn't specify a scheme
- You should always write the scheme, host, and mimetype using lowercase letters


# What's Next ?

Deep-Links-vs-Android-App-Links

https://github.com/KarimRedaHassan/Deep-Links-vs-Android-App-Links

# Android App Links Series

Understand-The-URL
- https://github.com/KarimRedaHassan/Understand-The-URL

Deep-Links-vs-Android-App-Links
- https://github.com/KarimRedaHassan/Deep-Links-vs-Android-App-Links

Allow-Other-Apps-to-Start-Your-Activity
- https://github.com/KarimRedaHassan/Allow-Other-Apps-to-Start-Your-Activity

# Additional Resources

https://developer.android.com/guide/topics/manifest/data-element.html


# Also See

#### A Full List Of All My Tutorials

https://github.com/KarimRedaHassan?tab=repositories

