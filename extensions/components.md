# Components API Documentation

Components are UI blocks that can replace or be appended to sections of the Standard Notes desktop and web app. They allow us to do cool things like custom editors, nested folders, tag autocomplete, and utility bars in the editor pane.

Building a component is easily done using the our JavaScript Components library. All you have to do is build a single-page web app using any framework you like (plain, Angular, React, etc), then use our components library to interact with the main window to save and request data.

## Building a Component

In this example, we'll use our blank-slate AngularJS template to build a utility bar that counts the number of words in the current note.

(The AngularJS template just makes it easy to get started. You can also create a project from scratch that utilizes the [Components JavaScript library](https://github.com/sn-extensions/components-api).)

1. Clone the [blank-slate](https://github.com/sn-extensions/blank-slate) project from GitHub:

		git clone https://github.com/sn-extensions/blank-slate.git

2. Build the project:

		npm install
		bower install
		grunt
		grunt watch

3. Start a local web server to host the app. Here we'll use Python's SimpleHTTPServer:

		python -m SimpleHTTPServer 8000


Open `localhost:8000` in your browser. You should see the text "Blank Slate" on the page.


## Writing the App

1. In order to count the number of words in a note, the component needs access to the "working note", or the note the user is currently editing. In `app/js/controllers/home.js`, uncomment the relevant parts of the permissions so it looks like this:

		var permissions = [
			{
				name: "stream-context-item"
			}
		]


2. Uncomment the function `streamContextItem` so it looks like this:

		componentManager.streamContextItem(function(item){
			// perform updates in $timeout so Angular can $apply()
			$timeout(function(){
				$scope.item = item;
			})
		})

Whenever a change is made to the working note, the block in that function will be called automatically.

3. Under `$scope.item = item;`, add

		$scope.analyzeNote($scope.item);

4. Create a function `analyzeNote` that will count the number of words in the note.

		$scope.analyzeNote = function(note) {
			var s = note.content.text;
			s = s.replace(/(^\s*)|(\s*$)/gi,"");//exclude  start and end white-space
			s = s.replace(/[ ]{2,}/gi," ");//2 or more space to 1
			s = s.replace(/\n /,"\n"); // exclude newline with a start spacing

			$scope.wordCount = s.split(' ').length;
		}

5. Open `app/templates/home.html.haml` and add this line:

		%p
			Word Count:
			%strong {{wordCount}}

(Haml uses indentation to parse the text. Make sure tabs are preserved when you copy)

Save all changes, and make sure the `grunt watch` process catches those changes and builds the project. If not, you can manually build at any time by typing `grunt`.

## Installing

See [Publishing](/extensions/publishing.html).

If you'd like to see the finished product, switch to the `word-count` branch:

```
git checkout word-count
```

## Available Areas

Areas tell Standard Notes where to display a particular component. The current list of available areas are:

- `tags-list`: replaces the tags pane with a custom component. We use this for the Folders component.
- `note-tags`: replaces the editor pane's tags area with a custom component. We use this for autocomplete tags.
- `editor-stack`: adds custom-sized components in a stack in the editor pane. This does not replace any native modules but simply adds layers on top of the editor pane. We use this for the Action Bar and GitHub Push components.
- `editor-editor`: replaces the plain text editor with a custom editor. We use this for all of our editors, including Markdown, Code, and Plus.

## Next Steps

There's an unlimited number of things you can build with components, that do anything from nested folders in the tags pane and autocomplete in the editor pane, to pushing notes to GitHub or WordPress.

To see how we built [our own components](https://standardnotes.org/extensions), study the source code [available here](https://github.com/sn-extensions).

For questions on development, [post in the forum](https://forum.standardnotes.org) or [join our Slack](https://standardnotes.org/slack).

If you'd like to support Standard Notes and use our secure hosting to install all the components we have to offer, consider purchasing the [Extended subscription](https://standardnotes.org/extended).
