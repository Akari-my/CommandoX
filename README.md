
<img width="2048" height="272" alt="minecraft_title" src="https://github.com/user-attachments/assets/4ba045fc-cd13-4574-a7cf-34aeb0c92a93" />

CommandoX is a command framework for **PocketMine‑MP** that makes it easy to build commands and subcommands with **typed arguments**, **permission checks**, and **automatic usage/error handling**.

---

## ✨ What CommandoX Does

CommandoX replaces manual `onCommand()` parsing with a simple class‑based API:

- **Root commands** via `BaseCommand`
- **Subcommands** via `BaseSubCommand`
- **Execution context** via `CommandContext`
- **Typed arguments** via `CommandArgument` implementations

It handles for you:

- Checking command & subcommand permissions
- Parsing and validating arguments (string, int, bool, player, raw text, enum, …)
- Sending clear error and usage messages when arguments are missing or invalid

You just:

1. Extend `BaseCommand` for your main command (e.g. `/example`)
2. Extend `BaseSubCommand` for each action (e.g. `/example hello`, `/example reload`)
3. Register arguments using provided argument classes

---

## 🧱 Core Components

- **`CommandoX\BaseCommand`**  
  Base class for a root command (registered in the PocketMine command map).

- **`CommandoX\BaseSubCommand`**  
  Base class for a subcommand (e.g. `/maincmd sub`).

- **`CommandoX\CommandContext`**  
  Object passed to `onRun()` with:
  - `getPlugin()`
  - `getSender()`
  - `getArgs()` / `getArg("name", $default)`
  - `getRootCommand()`, `getSubCommand()`

- **`CommandoX\CommandArgument` + built‑ins (`CommandoX\argument\*`)**  
  Typed arguments with built‑in implementations:
  - `StringArgument` – single word string
  - `IntegerArgument` – integer with optional min/max
  - `BooleanArgument` – true/false
  - `PlayerArgument` – online player by name/prefix
  - `EnumArgument` – one of a predefined set of values
  - `RawTextArgument` – rest of the line as text (with spaces)

---

## 📦 How to Use in your Plugin

Inside your plugin folder:

1. Create a directory called `lib`:
2. Put the CommandoX source tree inside lib/


In your main plugin class:
```php
use Example\command\ExampleCommand;

class Main extends PluginBase {

    public function onEnable(): void {
        // Register /example
        $this->getServer()->getCommandMap()->register("exampleplugin",new ExampleCommand($this, "example"));
    }
}
```
Example 1 – Simple Root Command
```php
<?php

namespace ExamplePlugin\command;

use CommandoX\BaseCommand;
use CommandoX\CommandContext;
use pocketmine\plugin\Plugin;

class ExampleCommand extends BaseCommand {

    public function __construct(Plugin $plugin, string $name = "example") {
        parent::__construct($plugin, $name, "Example command", []);
    }

    protected function configure(): void {
        $this->setPermission("exampleplugin.command.example");
        $this->setPermissionMessageCustom("§cYou don't have permission to use /example.");
    }

    public function onRun(CommandContext $context): void {
        $sender = $context->getSender();
        $sender->sendMessage("§aHello");
    }
}
```
Example 2 – Root Command with a Subcommand
```php
<?php

namespace ExamplePlugin\command;

use CommandoX\BaseCommand;
use CommandoX\CommandContext;
use MyPlugin\command\sub\GreetSubCommand;
use pocketmine\plugin\Plugin;

class ExampleCommand extends BaseCommand {

    public function __construct(Plugin $plugin, string $name = "example") {
        parent::__construct($plugin, $name, "Example command", []);
    }

    protected function configure(): void {
       $this->setPermission("exampleplugin.command.example")
       $this->setPermissionMessageCustom("§cYou don't have permission to use /example.");

        // Register subcommand: /example greet <name>
        $this->registerSubCommand(new GreetSubCommand($this->plugin));
    }

    public function onRun(CommandContext $context): void {
        $sender = $context->getSender();
        $sender->sendMessage("§eUse: §f/example greet <name>");
    }
}
```
Subcommand – GreetSubCommand
```php
<?php

namespace ExamplePlugin\command\sub;

use CommandoX\argument\StringArgument;
use CommandoX\BaseSubCommand;
use CommandoX\CommandContext;
use pocketmine\plugin\Plugin;

class GreetSubCommand extends BaseSubCommand {

    public function __construct(Plugin $plugin) {
        parent::__construct($plugin, "greet", "Greet a player by name");
    }

    protected function configure(): void {
        $this->setPermission("myplugin.command.example.greet");
        $this->setNoPermissionMessage("§cYou can't use /example greet.");

        // /example greet <name>
        $this->registerArgument(0, new StringArgument("name", false));
    }

    public function onRun(CommandContext $context): void {
        $sender = $context->getSender();

        $name = (string)$context->getArg("name", "Steve");
        $sender->sendMessage("§aHello, §e{$name}§a!");
    }
}
```
## 🙌 Contributing
All kinds of help to improve CommandoX are welcome:
- Bug reports and issue discussions
- Suggestions for new features (arguments, utilities, docs)
- Pull requests with improvements, refactors, or tests

If you like the project, feel free to open issues, send PRs, or share feedback to make CommandoX better for everyone
