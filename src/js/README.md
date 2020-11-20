# JavaScript Random Bits

You guessed it right! This folder contains random JavaScript bits and nuggets you can use for control add-in development in AL (or perhaps more, who knows...)

## GetImageResource.js

This file addresses the problem with referencing image resources from your control add-ins in AL workspaces.

Before AL, when control add-ins were only using the resource zip files, there was a hard requirement to include all scripts, stylesheets, and images in Script, StyleSheet, and Image folders (respectively). If you had an image called `foo.png` (and it had to reside inside the Image subfolder of your resource zip file), you would retrieve its runtime URL like this:

```javascript
const fooUrl = Microsoft.Dynamics.NAV.GetImageResource("foo.png");
```

When you move your control add-in into AL, that line of code will not work anymore. It would only work if you put your `foo.png` file into the root of your AL workspace, which is a very bad practice. Most likely, it would reside somewhere inside the `src\FooControl\Resources\Images` path (or something of the sort). Since with AL we can have better levels of organization of our code, and we don't have any hard requirements where exactly scripts, stylesheets, or images are, we would be putting them where it makes most sense for us. To retrieve the `foo.png` URL from that folder in an AL control add-in, you now have to do it like this:

```javascript
const fooUrl = Microsoft.Dynamics.NAV.GetImageResource("src\\FooControl\\Resources\\Images\\foo.png");
```

(The path is always relative to the project root)

Often, people restructure their repos. As your repo grows, you may decide to move your file from `src\FooControl\Resources\Images` into `src\SharedResources\Icons\Small`. When you do so, you again break your URL retrieval code.

The `GetImageResource.js` file fixes this problem for you. This is how you use it:

1. Include it somewhere in your project.
2. Change the `const pathToThisFile = "/Scripts";` line to match the path where the `GetImageResource.js` resides, relative to the root of your control add-in.
3. Add `GetImageResource.js` script to the `Scripts` declaration of your `controladdin` object.
4. Change the path of all your `Microsoft.Dynamics.NAV.GetImageResource` instances to be relative to your control add-in root (that is the path of your `controladdin` object file)

That's it! This file will then intercept any call to `Microsoft.Dynamics.NAV.GetImageResource` and will give you the correct URL. If you decide to restructure your project, this script will make sure you don't have to update any instances or references inside any of your script files - they will just continue to work! Just remember, if you change the path to the `GetImageResource.js` file inside your project, you'll have to update the `pathToThisFile` constant inside.

For more info about this one, check out my live stream recording at https://www.youtube.com/watch?v=Go7P3Ol5Z0Q
