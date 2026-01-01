


`TagMaster` brings tagging in Directory Opus to another level. It gives you an easy to use interface where you can manage them through a database, add them or remove them from files with a single click, plus edit descriptions and ratings. You can run it linked to your current file display selection or in a fuller standalone mode for batch work.
<p align="center">
<img width="1501" height="842" alt="Full Mode" src="https://github.com/user-attachments/assets/ac288269-d435-498c-953b-ca01f879d587" />

<img width="407" height="664" alt="Linked Mode" src="https://github.com/user-attachments/assets/36c6dabf-921f-4562-a43f-adf2b2764ff1" />
</p>
### Features
* Global tag database for searching and reusing tags across files.
* Cloud-like interface for tags with quick visual browsing and single-click add/remove.
* Search field to find tags fast.
* Bulk apply and "Add all" to tag many files at once.
* Per-file description editor for short captions or notes.
* Star rating editor integrated in the panel.
* Two operation modes: Linked (follows Opus selection) and `FULL` (managed file queue).
* Preview and properties panes so you can confirm content before applying metadata.
* Apply / Reset workflow to stage changes before committing.
* Optional wildcard `FILTER` preselection for easy file management selection.
* "No-block" concept applied when reading metadata. So you can cancel the command at any time instantly.
* AutoTag: automated analysis for images and PDFs to generate suggested tags and a short description (read **About AutoTag** section).

### Requirements

* Directory Opus 13.20 or newer.


### How to Install
Download the file from releases and double click it. [Or follow the instructions](https://resource.dopus.com/t/how-to-use-buttons-and-scripts-from-this-forum/3546/2#p-145908-script-add-ins-4)

### Usage
Run the command by using `TagMaster`.

By default the dialog `links` to the current selection: pick files in Opus and the panel shows their tags, description input, preview, and rating values. There's also the `FULL` mode dialog, who has more additions and where you can work with any passed file and drop files in to add them to the batch.

In `FULL` mode, when folders are passed, they are read recursively. Otherwise they are ignored.

##### Tagging

There are 2 panels in the window: one that shows the tag database (which has a filter at the top) and another panel that lists the **common tags** for the selected files.
Click any tag in the database to add it to the selection.
To remove a tag from the selection, click the ⓧ on the tag you want to remove in the list panel.
To remove ALL tags from the selection, click the ⓧ in the list panel and confirm in the following dialog.

To remove a tag from the database, click its corresponding ⓧ and confirm in the following dialog.

You can add ALL tags listed in the tags panel using the "Add All" button. (Be careful where you use it.)
You can also add new tags that are not in the database. To do that, perform a search and the script will automatically detect if the tag is new, giving you the option to add it to the database and to the selection. You can add multiple tags if you separate them with ";".

Click the Options icon (cog) in the tag database panel to access options:

<img width="380" height="172" alt="Options menu" src="https://github.com/user-attachments/assets/3eff2d24-d38b-49d1-8dc2-df710e702474" />

* Add Tags from items to database: By default, the script adds every tag it finds to your database. Disabling this option makes the database read-only.
* Import from Opus database: Opus has its own tag database. This option lets you import it for use.

##### Setting rating

You can also set the rating for the selection simply by clicking the rating you want.
Press the ⓧ button to clear the rating.
When multiple files are selected with different ratings, the text "multiple values" will be shown.

##### Setting description

You can set a description only when there is exactly 1 file in the selection.

##### Reset

Pressing this button will reset all values to their initial state (the state when the file was first read).

##### Apply

Press the button to apply any changes you made, if any. Note that in `LINKED` mode, saving is always automatic after changing the selection or closing the dialog.

In `FULL` mode, if you try to close the dialog with unsaved changes, you will be asked if you want to save them before exiting.

##### Filter

If the `FILTER` argument is used, files will be filtered using Opus wildcard syntax, so only files matching the given pattern are included.
In `FULL` mode, the filter is applied recursively to the contents of folders.

### Command arguments

Available arguments are:

| Argument  | Type | Value | Description                                                                                                         |
| --------- | ---- | ----- | ------------------------------------------------------------------------------------------------------------------- |
| FILES     | /K/M |       | Sets the files to act on in FULL mode. If no files are specified, the ones selected in the active tab will be used. |
| FULL      | /S   |       | Open the command in FULL mode.                                                                                      |
| FILTER    | /R   |       | Pass a pattern using Opus wildcard syntax.                                                                          |
| NOADDTODB | /S   |       | Makes the command's tag database read-only.                                                                         |

### Technical notes

* This add-in introduces 2(+1) novel concepts for scripts:

  * Tertiary processing: All metadata reading is done separately from the main process (like in another command), with both processes communicating transparently to the user. This prevents the UI from freezing during heavy loads and allows stopping the command at any time (except while saving files). It also lets the UI show information as it becomes available, without having to finish processing the entire file stack, giving a smoother feel compared to other add-ins. Very useful when reading folders.
  * Link to multi listers: The command can link to the current selection across multiple listers and respond smoothly for a JScript command, thanks to the use of timers and other implementations that showcase the scripting API's power.
  * AutoTagging using AI: Explained in the "About AutoTag" section.
* Files marked as "online only" are ignored in all cases.
* The command uses an in-memory cache to speed things up and optimize resources. Once a file is read, it stays cached while the command runs. It can also detect file changes and will automatically update its data.
* Folders are read recursively in `FULL` mode, and the filter is applied if set.
* In `LINKED` mode you can also use a filter, though it's not recommended because it can be confusing if you don't fully understand how it works.
* The command is NOT meant for files where the path type is shell|mtp|ftp|plugin|zip.
* Most strings in the configuration dialog depend on the current Opus language. Any strings not translated will appear in English for most users unless you run Opus in Spanish (Mexico).

### About AutoTagging

When used in `FULL` mode, this command can use AI models (the most popular ones) to "understand" images and PDFs and suggest descriptions and tags.

Originally this script was intended for my personal use, and it was tightly integrated with another add-in that, among other things, lets you manage models and their API keys. Since that other script isn't finished, this one lacks a UI for managing models and API keys.

As a workaround, this script includes several known models by default. To use them, you must have the corresponding API keys set in `environment variables`. This is common practice, so if you already have keys stored, you can use this feature (the variable names should be the standard ones, e.g. `OPENAI_API_KEY`). I'm not going to explain here how to obtain or store them in envvars, there's plenty of detailed info online.

When the command starts it will detect if you have any keys installed and enable the corresponding models. In that case, when you select a single .png, .jpg, .webp, or .pdf file, the AI button is enabled. Next to it you can choose from several enabled models. **Note**: the file must be under 10 MB because it is sent base64-encoded to avoid creating a persistent object accessible by ID (and to avoid using the API to upload larger files).

Once the request is made, the next window shows the results if there's a response.

<img width="407" height="439" alt="AutoTag AI window" src="https://github.com/user-attachments/assets/92b0f8b3-2a8a-4053-8651-b56f933c82b6" />

Results can be edited, discarded, or accepted.



**Note 1** : This script does NOT upload any file to AI service servers automatically, nor does it misuse your credits. If you use it, you are fully responsible for the content you send, which is why the process is intentionally manual.
**Note 2** : Images are sent "as is", without resizing or metadata cleaning. This can be improved depending on user feedback (personally I use it for tasks where these parameters don't matter, so I didn't implement that).

The hardcoded models and the envvar they look for are:
| Model              | ENV VAR | 
|--------------------| ----------------|
|Mistral Pixtral 12B | MISTRAL_API_KEY |
|Mistral Pixtral Large | MISTRAL_API_KEY |
|Gemini 2.5 Flash | GOOGLE_API_KEY |
|Gemini 2.5 Flash Lite | GOOGLE_API_KEY |
|Gemini 3.0 Flash Preview | GOOGLE_API_KEY |
|Gemini 3.0 Pro Preview | GOOGLE_API_KEY |
|OpenAI 4.1 | OPENAI_API_KEY |
|Gemini 2.0 (OpenRouter) | OPENROUTER_API_KEY | 
|Gemma3 27B (OpenRouter) | OPENROUTER_API_KEY |

### If you want to help

If you're interested, try the script and report edge cases or any suggestion you might have. Feedback will guide the next improvements.

*Any suggestions or test results are appreciated.* :wink:

Currently the dialog are full translated to English and Spanish (Mexico). If you want to help to translate the dialog into your current language, feel free to send me a message.

### Credits / Acknowledgments
* GPSoftware team for Directory Opus.

### Disclaimer
This is an independent project that uses third-party AI services and APIs.

This project is not affiliated with, endorsed by, or sponsored by any of them.  
All product and company names are trademarks or registered trademarks of their respective owners.

