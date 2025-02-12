![mybible-cli icon](icons/icon.png "mybible-cli icon")
# About

`mybible-cli.py` is a command line utility for querying [MyBible.zone](https://mybible.zone/en/) modules. It was inspired by [diatheke](https://wiki.crosswire.org/Frontends:Diatheke) — a command line utility for [Crosswire Sword](http://www.crosswire.org/sword/index.jsp) modules.  
The main goal of `mybible-cli.py` is just to get the required text, not to perform searches or analyze the matched data, as there are other tools that can do that. The output of the tool can be piped, so there are unlimited possibilities to do whatever you want with the output text. Although the MyBible module format specification describes more than one module type, the script only works with Bible modules. All other modules (commentaries, devotions, plans, etc.) are invisible to the script. A good place to get modules is the [ph4.org  website](https://www.ph4.org/b4_index.php?hd=b).

# Installation

`mybible-cli.py` is a self-contained script with no dependencies on anything other than Python 3.12. No installation is required, the script can be run from anywhere. However, if you find it useful, you may want to copy or symlink it somewhere in your $PATH (%PATH%).  
Here's one way to do it:

```  bash
git clone https://github.com/kosivantsov/mybible-cli.git
cd mybible-cli
chmod +x mybible-cli
ln -s $(pwd)/mybible-cli.py $HOME/bin/mybible-cli
```

This way, you could run it by simply executing `mybible-cli`. The script would automatically update when you pull changes in your local git copy.

If you're using MS Windows and don't have Python installed, it's still possible to use the script. [Download]((https://github.com/kosivantsov/mybible-cli/releases/latest/) the Windows build (look for a file called `mybible-cli.zip`), unzip it somewhere, and run `mybible-cli.exe` in the unzipped folder. When the zip is unzipped, you'll find `mybible-cli.exe` and a subfolder called `_internal`. Both are needed to run the program.

For the program to be useful (either as a script or as a Windows executable), you must have at least one MyBible module.

# Usage

This script/application can be used directly on the command line, or used in other [scripts and applications](./tools) to get Bible text. Below is a brief overview of how to use it on the command line.

## First run

When it is run for the first time (unless `-h`, `--help`, or `--helpformat` arguments were used), it will ask for a path to the folder containing MyBible modules. The modules folder can be changed at any time with the `-p` or `--path` argument.  
When run for the first time with `--gui`, a GUI file chooser will open to select the modules folder. 

## Help messages

Running the script without any arguments will display a short help message. Run it with `-h`, `--help`, `--helpformat` to get more details.
<details>
  <summary>Long help message</summary>
<code bash>Options:
  -h, --help
        Shows help message and exits
  -p PATH, --path PATH
        Specify the path to the folder with MyBible modules
  -L, --list-modules
        List available MyBilbe modules
  -m MODULE_NAME, --module-name MODULE_NAME
        Name of the MyBible module to use
  -r REFERENCE, --reference REFERENCE
        Bible reference to output
  -a ABBR, --abbr ABBR
        Get Bible book names and abbreviations from a non-default file.
        With '--abbr uk' a file named 'uk_mapping.json' located in the configuration folder will be used
  -A, --self-abbr
        Get Bible book names and abbreviations from the module itself
  -f FORMAT, --format FORMAT
        Format output with %-prefixed format sting.
        Available placeholders: %f, %a, %c, %v, %t, %T, $z, %A, %Z, %m
  -F SAVE_FORMAT, --save-format SAVE_FORMAT
        Specified format string will be applied and saved as default.
  --helpformat
        Detailed info on the format string
  --noansi
        Clears out any ANSI escape sequences in the Bible verses output (if %A or %Z were used in the format string)
  --open-config-folder
        Opens the config folder
  --open-module-folder
        Opens the folder with MyBible modules
  --j2t &lt;JSON_FILE&gt;, --json-to-tsv &lt;JSON_FILE&gt;
        Converts a json file to tsv (to edit a mapping file)
  --check-tsv &lt;TSV_FILE&gt;
        Reports duplicates in the specified tsv file
  --t2j &lt;TSV_FILE&gt;, --tsv-to-json &lt;TSV_FILE&gt;
        Converts a tsv file to json (to use as a mapping file)</code>
  --gui
        Outputs text in a GUI window
</details>

## Listing available modules

The script can list all the installed modules with `-L`. The list will be sorted by language and will only include Bible modules. When first invoked, it would take a few moments to query each file and get the needed info. This information is then cached and reused until the modules are changed.

<div align="center"><img src="screenshots/list.png" width="50%" alt="List" title="List"></div>


## Getting text of a Bible reference

The most common use would be to call the script with a module name and reference to get the desired text:  
`mybible-cli -m "KJV+" -r "Jn 11:35"`  
If a parameter passed to the script contains a space or a character that may have a special meaning to the shell, it must be quoted.  
The script understands only colon Bible notation without letters and parentheses in the chapter and verse part. Chapter and verse numbers can be omitted to output an entire book or chapter. Separate blocks of text should be separated by commas or semicolons. Ranges are indicated by a minus sign. Spaces are allowed in ranges. Periods are ignored.  
If `-m "<MODULE_NAME>"` is omitted in the command, the script will use the last module used.
`mybible-cli -m "KJV+" -r "Jn 11:35" --gui` will output the text in a GUI window where it is possible to view the requested text in any of the installed modules without running the command again.


## Output format

The script outputs each verse on a separate line and formats it using a format string with %-prefixed placeholders.  
To learn what each placeholder means, run `mybible-cli --helpformat`.

The default format is <strong>`%f %c:%v: %t (%m)`</strong> (full book name, chapter:verse\: text without most of the MyBible markup, module name in parentheses): `John 11:35: Jesus wept. (KJV+)`.
A format string specified with `-f` is applied only once. With `-F`, the specified format string is saved as default and used if no format string is explicitly given.

The reference text can be output in five different ways:
1. `%T` - raw text with all the markup as in the module itself
1. `%t` - plain text, no Strong's numbers, no other markup, but with line breaks and indentations as marked in the module; with notes
1. `%z` – the same as above, but without line breaks and indentations as marked in the module; notes removed
1. `%A` – MyBible markup is converted to ANSI escape sequences for pretty terminal output. Includes Strong's numbers
1. `%Z` – the same as above, but without Strong's numbers  
   If you need Strong's numbers in the output, but don't want to get the escape sequences (e.g. if you pipe the output of the script), there is an option `--noansi`. It has no effect on the output if the text is not formatted with `%A` or `%Z`.

![Raw text](screenshots/raw_text.png "Raw Text")
![Default output](screenshots/default_output.png "Default output")
![ANSI colors](screenshots/ansi_colors.png "ANSI colors")

The GUI output can also be formatted, either on the command line or by specifying the format string in the corresponding dialog box. The format string specified in the GUI is saved as the default.

![GUI window](screenshots/GUI_window.png "GUI Window")


## Bible book names and abbreviations

Bible book names and abbreviations are looked up in a list provided within the script. On the first run, the list is saved as `mapping.json` in the configuration directory and can be used to create custom lookup lists. This file maps Bible book codes specific to the mybible.zone format to possible book names, both full and abbreviated.

If you want to use book names and abbreviations from the module itself, run the script with the `-A` argument. To use a non-default lookup list, use the `-a prefix` option. In this case, the script will try to use `prefix_mapping.json` in the config folder.
`prefix` can be an arbitrary string, but there should be a file in the config folder with a filename that starts with this string and is immediately followed by `_mapping.json`, otherwise the default lookup file will be used.  
The `mybible-cli.py` script doesn't validate the mapping file specified by the user.

The script takes three arguments to help you create custom files to look up the names of the books of the Bible:
* `--j2t`, `--json-to-tsv`: converts json to tsv which can be opened and edited in a spreadsheet application
* `--t2j`, `--tsv-to-json`: converts tsv with edited data to json which can be used with the `-l` argument
* `--check-tsv`: finds and reports duplicates in a tsv file

`json-to-tsv` and `tsv-to-json` will output the converted file in the same location as the input file, with the same filename but a different extension. No file extension or data validation is performed during conversion, so it's possible to convert wrong data into wrong formats which can't be used by the script as mapping data.


## Accessing config and MyBible modules folders

The script allows to open its config folder and the folder with the MyBible modules in the default file manager. It takes two arguments for that:
* `--open-config-folder` (to open the config folder) 
* `--open-module-folder` (to open the module folder)


## Localized version of the script

The UI strings of this script can be localized. The script reads its localization from a file `l10n/<lang>.properties` located in the configuration folder, where `<lang>` is a language code. If there is a .properties file with the same filename as the system locale language code, that file will be used. Any strings missing from the file will be displayed in English (the script itself contains UI strings for such fallback cases). Currently only `en.properties` (English) and `uk.properties` (Ukrainian) are available. To install new translations for the script, localization files must be manually copied to the `l10n` folder in the config folder.  

# Building an executable to run without Python installation

The script doesn't require to be built on GNU/Linux and macOS since these OSes have Python installed by default. On Windows with Python installed the script can run as is, without building.
If you need to build a binary that will run without Python being installed on your system, it can be done with `pyinstaller`. Included you'll find the spec file. `pyinstaller` is not needed for the script to function,
it's only purpose is to compile the script into a binary.
1. Create a new venv: `python3.12 -m venv $HOME/pyinstaller-venv`
1. Activate the venv: `source $HOME/pyinstaller-venv/Scripts/activate` or `cd $HOME; .\pyinstaller-venv\Scripts\activate`
1. Install pyinstaller: `pip3 install pyinstaller`
1. Go to `mybible-cli` source folder and build the binary: `cd src/mybible-cli; pyinstaller mybible-cly.spec`

On Windows you may also use `buildWindows.cmd` that automates the process.

A folder with the executable and its dependencies will be found under `dist` subfolder.
