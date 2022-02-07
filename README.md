[![Version: 1.0.0](https://img.shields.io/badge/Version-1.0.0-blue)][VCC]
[![Android: 5.0+](https://img.shields.io/badge/Android-5.0%2B-blue)][Tasker]
[![Tasker for Android: 5.9.3+](https://img.shields.io/badge/Tasker%20for%20Android-5.9.3%2B-blue)][Tasker]
[![License: GPL 3](https://img.shields.io/badge/License-GPL%203-green)](LICENSE)

[Tasker] project: Voice Command Capture ([VCC])
======================================================

[VCC] is an Android [Tasker] project that allows to capture voice commands and dispatch them to other tasks to be processed. This includes the ability to intercept voice assistant commands.

For example, using this project you can selectively capture into [Tasker] any commands you say to Google Assistant, even commands that contain parameters, without having to define Google Assistant routines, without the need to specify the "_run in Tasker_" integration.

- [Requirements](#requrements)
- [Installation and setup](#installation-and-setup)
- [Configuration](#configuration)
- [Command sources](#command-sources)
  - [Tasker voice command](#tasker-voice-command)
  - [Assistant Action command](#assistant-action-command)
  - [Autovoice command](#autovoice-command)
  - [Google Assistant command](#google-assistant-command)
    - [Additional setup and configuration](#additional-setup-and-configuration)
    - [Notes on Google Assistant interception](#notes-on-google-assistant-interception)
- [Important notes](#important-notes)

<br/>

# Requirements

- [Tasker] 5.9.3 or newer.
- [AutoInput] plugin for [Tasker].
- [Tasker] 5.11.14 or newer, if you want to capture [Assistant Action commands](#assistant-action-command).
- [AutoVoice] plugin for [Tasker], if you want to capture [AutoVoice commands](#autovoice-command).

# Installation and setup

To get started:

1. Import the **[vcc project]** into Tasker.
2. [Configure](#configuration) the dispatch tasks.

If you want to intercept [GoogleAssistant commands](#google-assistant-command):

3. Import the **[vcc_locale project]** into Tasker.
4. Follow the related [additional setup and configuration](#additional-setup-and-configuration) instructions.

Finally:

5. Enable/disable the project profiles, depending on the [command sources](#command-sources) you want to intercept.
6. Read the [important notes](#important-notes) section, as it also contains relevant setup information.

# Configuration

Configuration variables can be set in the task **VCC - Config - Dispatcher**.

- ```%cfg_dispatch_task```

  This is an array of task names that you want the captured commands to be sent to.

  For example, you can specify several tasks to receive the captured commands by setting those tasks names in the variables ```%cfg_dispatch_task1```, ```%cfg_dispatch_task2```, ...

  You should specify at least one existing task.

  **IMPORTANT**: These tasks must be a bit cooperative.

  - The called task receives the captured command as first parameter ```%par1```.

  - The called task MUST return a value of ```1``` to signal that it recognized the command passed to it, and otherwise return ```0``` or nothing. There is no other way for the dispatcher to know if a command has been recognized or not and act accordingly.

- ```%cfg_dispatch_multiple```

  This is a boolean value that determines if a captured command will be processed by one or more tasks.

  If you specify a value of ```0``` (this is the default) the dispatcher will stop as soon as a task recognizes a command, so that captured command will not be sent to the rest of tasks.

  A value of ```1``` means that the captured command will be sent unconditionally to all configured tasks.

If you need clarification on topics like how to set a variable, manage task parameters or return a value from a task, etc., please refer to the [Tasker documentation].

# Command sources

The next command sources can be intercepted:

## Tasker voice command

Create a shortcut to the **VCC - Capture - Voice** task in your device desktop.

Every time you run this task, [Tasker] will ask you for a voice command to dispatch.

## Assistant Action command

Enable the **VCC - Intercept - Assistant Action** profile.

You can then capture and dispatch voice commands by saying:

"_Hey Google, run \<YOUR-COMMAND-HERE\> in Tasker_"

## Autovoice command

Enable the **VCC - Intercept - AutoVoice** profile.

You can then capture and dispatch voice commands by saying:

"_Hey Google, ask AutoVoice to \<YOUR-COMMAND-HERE\>_"

## Google Assistant command

Enable the **VCC - Intercept - Google Assistant** profile.

You can then capture and dispatch voice commands by saying:

"_Hey Google, \<YOUR-COMMAND-HERE\>_"

### Additional setup and configuration

Intercepting a Google Assistant command works by continuously checking the texts displayed in the Google Assistant window while it is opened. But these texts are localized in your system´s language, so to achieve this some additional setup and configuration is required.

- Import into Tasker the additional **[vcc_locale project]**.

  This additional project defines the text patterns used by [VCC] to recognize when the Google Assistant is opened and when it does or does not contain any command to capture.

  A task is defined for every supported language. If your language is not supported yet, you should create a task specific for your language (use the ones in other idioms as a guide). You can then send it to the project developer to make the list of supported languages grow.

  These text patterns are subject to changes if the Google Assistant interface changes, so they are maintained apart from the rest of the project.

- Configuration variables can be set in the task **VCC - Config - Google Assistant**.

  - ```%cfg_locale_force```

    [VCC] does its best to determine the locale that is being used to display Google Assistant messages. If that fails, you can bypass the locale detection by specifying your language locale in this variable.

  - ```%cfg_locale_default```

    If your locale is not supported yet, [VCC] will try to check the texts in Google Assistant window using this default locale, if specified.

  - ```%cfg_check_interval```

    While Google Assistant is opened, [VCC] will periodically check for a command to capture. This variable defines the number of seconds between checks (by default will check every ```1``` second).

  If you need clarification on topics like how to set a variable, please refer to the [Tasker documentation].

  ### Notes on Google Assistant interception

  Please note that intercepting and capturing a command from the Google Assistant visual interface does not prevent Google Assistant from also processing it.

  For example, if you say a command like "_Hey Google, turn down volume_" the command will be intercepted and dispatched, but the Google Assistant will ALSO turn down the volume in your device.

# Important notes

- [AutoInput] requires to be enabled as an Android accesibility service to be able to check for texts shown in Google Assistant.

  You can enable it by opening [AutoInput] and pressing the "**Accesibility**" option.

  Beware that some Android flavors eventually disable some accessibility services for no clear reason.

- When used by [VCC], [AutoInput] shows error notifications in the Android notifications bar when it detects that Google Assistant has been closed (something that is necessary). To get rid of those unwanted notifications there are two options:

  - Disable [AutoInput] error notifications. To achieve this open [AutoInput], and under "**Logs**" configuration deactivate the option "**Action Error Notifications**".

  - Enable the **VCC - Aux - AutoInput error** profile in [Tasker]. This will require that you have the [AutoNotification] plugin for [Tasker] installed.

- The system language detection may be imprecise if you have more than one locale language installed in your Android device. If you find that the texts in your language are defined but [VCC] is not using them, try to force the language locale used by [VCC] using the configuration variables set in the **VCC - Config - Google Assistant** task.

- If [VCC] stops capturing commands, check that your Android battery saver is not killing any of the programs required for [VCC] to work. That includes [Tasker] and all plugins involved in the process.

- In configuration variables and task names, locales are specified following the [ISO 639-1](https://wikipedia.org/wiki/ISO_639-1) standard. For example, "**en**" stands for any engilsh variant, "**en-US**" for the USA english variant, "**es**" stands for any spanish variant, "**es-ES**" for the Spain spanish variant, and so on.


[VCC]: ./README.md#top
[Tasker documentation]: https://tasker.joaoapps.com/
[Tasker]: https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm
[AutoInput]: https://play.google.com/store/apps/details?id=com.joaomgcd.autoinput
[AutoVoice]: https://play.google.com/store/apps/details?id=com.joaomgcd.autovoice
[AutoNotification]: https://play.google.com/store/apps/details?id=com.joaomgcd.autonotification

[vcc project]: https://taskernet.com/shares/?user=AS35m8nuYECCRZX6Z3dLemrH8t2K4h9Dkn9B%2Fr8F9Ydfsp0xZ%2BgRGC87lUwDzJgqI09t65c%3D&id=Project%3AVoice+Command+Capture

[vcc_locale project]: https://taskernet.com/shares/?user=AS35m8nuYECCRZX6Z3dLemrH8t2K4h9Dkn9B%2Fr8F9Ydfsp0xZ%2BgRGC87lUwDzJgqI09t65c%3D&id=Project%3AVoice+Command+Capture+-+Localization
