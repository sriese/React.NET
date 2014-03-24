React.NET
=========
React.NET is a library that makes it easier to use Facebook's
[React](http://facebook.github.io/react/) and 
[JSX](http://facebook.github.io/react/docs/jsx-in-depth.html) from C#.

Features
=========
 * On-the-fly **JSX to JavaScript compilation** for development purposes
 * JSX to JavaScript precompilation via 
   [ASP.NET Bundling and Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
 * **Server-side component rendering** to make your initial render super-fast
   (experimental!)

Requirements
============
 * ASP.NET 4.0 or higher
 * ASP.NET MVC 4 (support for other versions will come eventually)
 * [JSON.NET](http://james.newtonking.com/json)
 * A JavaScript engine:
   * [MsieJavaScriptEngine](https://github.com/Taritsyn/MsieJavaScriptEngine) -
     Windows only and requires IE9 or above to be installed on the server
   * ~~[Jint](https://github.com/sebastienros/jint) - Slower but cross-platform~~

Installation
============
Via released [NuGet package](#)
----------------------------
To be written

Via latest development build
----------------------------
To be written

Manual Installation
-------------------
1. Compile React.NET by running `build.bat`
2. Reference React.dll and React.Mvc4.dll (if using MVC 4) in your Web
   Application project
3. See usage example below

Usage
=====
Create your React components, ensuring you add the `/** @jsx React.DOM */` 
docblock.

```javascript
// HelloWorld.react.jsx
/** @jsx React.DOM */
var HelloWorld = React.createClass({
	render: function () {
		return (
			<div>Hello {this.props.name}</div>
		);
	}
});
```

On-the-Fly JSX to JavaScript Compilation
----------------------------------------
Hit a JSX file in your browser (eg. `/Scripts/HelloWorld.react.jsx`) and observe
the magnificence of JSX being compiled into JavaScript with no precompilation
necessary. Note: This is good for fast iteration during development, but for 
production you will want to precompile for best performance.

ASP.NET Bundling and Minification Support
-----------------------------------------
React.NET supports the use of Microsoft's 
[ASP.NET Bundling and Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
library to compile JSX into JavaScript and minify it along with all your other 
JavaScript. Simply create a `JsxBundle` containing any number of JSX or regular
JavaScript files:

```csharp
// In BundleConfig.cs
bundles.Add(new JsxBundle("~/bundles/main").Include(
	// Add your JSX files here
	"~/Content/HelloWorld.react.jsx",
	"~/Content/AnythingElse.react.jsx",
	// You can include regular JavaScript files in the bundle too
	"~/Content/ajax.js",
));
```

`JsxBundle` will compile your JSX to JavaScript and then minify it. For more 
control (eg. if you want to run other transforms as well), you can use 
`JsxTransform` directly:

```csharp
// In BundleConfig.cs
bundles.Add(new Bundle("~/bundles/main", new IBundleTransform[]
{
	// This works the same as JsxBundle (transform then minify) but you could 
	//add your own transforms as well.
	new JsxTransform(),
	new JsMinify(),
}).Include(
	"~/Content/HelloWorld.react.jsx"
));
```

Server-Side Component Rendering
-------------------------------
Modify `App_Start\ReactConfig.cs` to reference your components

```csharp
namespace MyApp
{
	public static class ReactConfig
	{
		public static void Configure()
		{
			ReactSiteConfiguration.Configuration = new ReactSiteConfiguration()
				.AddScript("~/Scripts/HelloWorld.react.jsx");
		}
	}
}
```

Call `Html.React` to render a component server-side, passing it the name of the 
component, and any required props.

```csharp
@Html.React("HelloWorld", new
{
	name = "Daniel"
})
```

Call `Html.ReactInitJavaScript` to render initialisation scripts. Note that this
does **not** load the JavaScript for your component, it only renders the 
initialisation code.

```csharp
<!-- Load all your scripts normally before calling ReactInitJavaScript -->
<script src="http://fb.me/react-0.9.0.min.js"></script>
<script src="~/Scripts/HelloWorld.react.jsx"></script>
@Html.ReactInitJavaScript()
```

Hit the page and admire the server-rendered beauty:

```html
<div id="react1">
	<div data-reactid=".2aubxk2hwsu" data-react-checksum="-1025167618">
		<span data-reactid=".2aubxk2hwsu.0">Hello </span>
		<span data-reactid=".2aubxk2hwsu.1">Daniel</span>
	</div>
</div>

<script src="http://fb.me/react-0.9.0.min.js"></script>
<script src="/Scripts/HelloWorld.react.js"></script>
<script>React.renderComponent(HelloWorld({"name":"Daniel"}), document.getElementById("react1"));</script>
```

The server-rendered HTML will automatically be reused by React client-side, 
meaning your initial render will be super fast.

For a more in-depth example, take a look at the included sample application (React.Samples.Mvc4).

Changelog
=========
1.0 - ??? 2014
-------------------
 - Initial release

Licence
=======
BSD License for React.NET

Copyright (c) 2014, Facebook, Inc. All rights reserved.

Redistribution and use in source and binary forms, with or without modification,
are permitted provided that the following conditions are met:

 * Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.
 * Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.
 * Neither the name Facebook nor the names of its contributors may be used to
   endorse or promote products derived from this software without specific
   prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON
ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
