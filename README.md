**Dolphin** is an innovative, automated internationalization (i18n) service designed to integrate seamlessly into your development pipeline, with the power of AI.

# Table of contents

<!--ts-->

- [Installation](#Installation)
- [Usage](#Usage)
- [Configuration](#Configuration)
- [Debugging](#Debugging)

<!--te-->

# Installation

**Homebrew**

```shell
brew tap therootlab/tap
brew install therootlab/tap/dolphin
```

Check if the installation was successful:

```shell
dolphin --help
```

# Usage

## Prepare

Dolphin requires a configuration file to desribe the translation strings and preferred options.

Here's an example of a configuration file:

```yaml
baseLanguage: en
translator:
  agent: openai
  mode: interactive
localizations:
  - path: TranditionalXcodeDemo/${LANGUAGE}.lproj/Localizable.strings
    format: strings
    languages:
      - fr
      - ja
      - zh-Hans
```

It means that Dolphin will look for a file named `Localizable.strings` in the `TranditionalXcodeDemo/en.lproj` directory whose format is `Apple Localized Strings`. And then translate it to French, Japanese, and Simplified Chinese with `openai`.

For more details on how to write a configuration file, please check the [Configuration](#Configuration) section.

## Running Dolphin

By default, Dolphin looks for a file named `dolphin.yml` in the current working directory.

```shell
dolphin localize
```

You can specify a different file with the `--config` option.

```shell
dolphin localize --config path/to/dolphin.yml
```

An interactive shell like below will guide you through the translation process.

![Output Screenshot](assets/output-screenshot.jpg)

## Configuration

Configration file is a YAML file with the following structure:

```yaml
baseLanguage: [language code]

translator: [translator name]
# or
translator:
  agent: [translator name]
  mode: [translator mode]

context: [context]
localizations:
  - path: [path]
    format: [format]
    languages:
      - [language code]
      - [language code]
      ...

  - path: [path]
    format: [format]
    languages:
      - [language code]
      - [language code]
      ...

  ...
```

Language codes can be any commonly used ones, such as `en-US`, `ja`, `es`, etc. As long as it is supported by the translator.

#### baseLanguage

The source language of the strings, which is used to translate from.

#### translator

Supported translators:

- **openai**: OpenAI API

Translator can be either a single string of the translator name, or an object with `agent` and `mode` properties.

Supported modes:

- **interactive**: In interactive mode, after strings are translated, Dolphin will ask you to review the result to approve or ask the agent to refine. By default, this mode is not enabled.

#### context

The context of the translation. It will be used to provide more information to the translator.

> For example, if you don't want specific words to skipped, you can say "xxx is a specific term which should be translated."

#### format

- **strings**: Apple Localized Strings (with `"key" = "value"` format)
- **text**: Plain text
- **xcode**: Xcode project (Using Xcode built-in localization tool)

#### path

The path to the localization file. You can use `${LANGUAGE}` as the language placeholder.

> For Xcode format, the path should be `.xcodeproj` folder.

#### languages

The target languages to translate to.

## Debugging

### Logs

By default, shell wil only show the major output. To see more details, you can check the log file at `~/Library/Logs/Dolphin/dolphin-[date].log`.

### Exported Strings

Dolphin will export the translated strings to the `export` folder in the system temporary directory, which will be displayed in the shell output. You can check if the exported strings are correct there.
