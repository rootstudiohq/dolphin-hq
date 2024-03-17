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
dolphin --version
```

# Usage

## API Keys

Dolphin requires API keys to access the translation services. You can get them from your dashboard on [dolphin website](https://dolphin.rootstudio.io).

**Be care, this is not openai key. During beta stage, you need request for api access by contacting hi@rootstudio.io**

Set the API keys as environment variables:

```
export DOLPHIN_API_KEY=SK-xxx
```

You can also set API key in the configuration file as described later in the [Configuration](#Configuration) section.

Make sure you don't expose the key to the public to avoid unexpected cost.

## Prepare

Dolphin requires a configuration file to desribe the translation strings and preferred options.

Here's an example of a configuration file:

```yaml
baseLanguage: en
translator:
  agent: openai
  mode: interactive
localizations:
  - id: hostapp
    path: TranditionalXcodeDemo/${LANGUAGE}.lproj/Localizable.strings
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
apiKey: [optional, api key]

baseLanguage: [required, language code]

translator: [required, translator name]
# or
translator:
  agent: [required, translator name]
  mode: [required, translator mode]

skipExisting: [optional, boolean, skip translated strings]

context: [optional, custom translation context]

localizations:
  - id: [required, string, to identify the localization]
    path: [required, path to strings]
    format: [required, string format]
    languages:
      - [required, language code]
      - [required, language code]
      ...

  - id: [required, string, to identify the localization]
    path: [required, path to strings]
    format: [required, string format]
    languages:
      - [required, language code]
      - [required, language code]
      ...

  ...
```

Language codes can be any commonly used ones, such as `en-US`, `ja`, `es`, etc. As long as it is supported by the translator.

#### apiKey

The API key to access the translation service. We recommend you to set it as an environment variable (`DOLPHIN_API_KEY`) instead of putting it in the configuration file for security reasons.

```
export DOLPHIN_API_KEY=SK-xxx
```

#### baseLanguage

The source language of the strings, which is used to translate from.

#### translator

Supported translators:

- **openai**: OpenAI API

Translator can be either a single string of the translator name, or an object with `agent` and `mode` properties.

Supported modes:

- **interactive**: In interactive mode, after strings are translated, Dolphin will ask you to review the result to approve or ask the agent to refine. By default, this mode is not enabled.

#### skipExisting

If set to `true`, Dolphin will skip the strings that have been translated. By default, this option is not enabled. It is useful when you want to translate new strings only.

Please note that this option checks language by language. For example, if you have a string targeting English and French, and you have translated the English one, Dolphin will still translate the French one.

You can also pass "--skip-existing" option to the command line to override this option.

#### context

The context of the translation. It will be used to provide more information to the translator.

> For example, if you don't want specific words to skipped, you can say "xxx is a specific term which should be translated."

#### id

Arbitary unique id across localizations, used to identify the specific localization in the project.

#### format

- **strings**: Apple Localized Strings (with `"key" = "value"` format)
- **text**: Plain text
- **xcode**: Xcode project (Using Xcode built-in localization tool)

#### path

The path to the localization file. You can use `${LANGUAGE}` as the language placeholder.

> For Xcode format, the path should be `.xcodeproj` folder.

#### languages

The target languages to translate to.

## Examples

For more examples, please check the [Examples](https://github.com/therootlab/dolphin-examples) repo.

## Debugging

### Logs

By default, shell wil only show the major output. To see more details, you can check the log file at `~/Library/Logs/Dolphin/dolphin-[date].log`.

### Exported Strings

Dolphin will export the translated strings to the `export` folder in the system temporary directory, which will be displayed in the shell output. You can check if the exported strings are correct there.
