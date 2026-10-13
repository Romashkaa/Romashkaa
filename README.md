# My Projects

I build tools for Python developers, focused on simplicity, safety, and minimal boilerplate.

## Telekit
[**Telekit**](https://github.com/Romashkaa/telekit) is a declarative framework for building Telegram bots. Instead of state machines, it uses **Chains**, so a dialogue reads like ordinary method calls. It handles inline keyboards and callback routing automatically, and also offers typed command parameters, a **Styles** API for formatting without manual escaping, reusable **Traits** (including a built-in calendar picker), and a built-in **DSL** with Jinja support for menus, FAQs, and quizzes with zero code.

Telekit is listed on [Telegram's official website](https://core.telegram.org/bots/samples) among the example libraries for bot development.

A dialogue without an FSM: just point to the next step:

```python
import telekit

class DialogueHandler(telekit.Handler):
    @classmethod
    def init_handler(cls):
        cls.on.command("start").invoke(cls.handle)

    def handle(self):
        self.chain.sender.set_text("👋 Hello! What is your name?")
        self.chain.set_entry_text(self.handle_name)
        self.chain.send()

    def handle_name(self, name: str):
        self.chain.sender.set_text(f"Nice to meet you, {name}!")
        self.chain.set_inline_keyboard({"↺ Restart": self.handle})
        self.chain.send()

TKOEN: str = telekit.utils.read_token(".env")

telekit.Server(TOKEN).polling()
```

Or skip Python entirely with the built-in DSL:

```js
@ main {
    title   = "🎉 Fun Facts Quiz";
    message = "Test your knowledge with 10 fun questions!";

    buttons {
        start_quiz("Start Quiz"); // -->  @ start_quiz { ... }
    }
}
```

## Pylits
[**Pylits**](https://github.com/Romashkaa/Pylits) is a fast, deterministic serialization format for pure Python literals (`bool`, `int`, `float`, `str`, `list`, `tuple`, `set`, `dict`, `None`).

- Very fast, with minimal parsing overhead
- Safe by design, a safer alternative to `eval()` for storing data
- Deterministic: the same data always produces the same output, even if dict key order or set order differs, which makes it suitable for hashing
- Zero dependencies
- Lossless round-trip: `decode(encode(x)) == x`

```python
import pylits

encoded = pylits.encode([1, "2", True])
assert pylits.decode(encoded) == [1, "2", True]
```

## Litsdb
**litsdb** provides persistent, type-safe collections backed by SQLite and Pylits serialization. You work with them like regular Python objects:

```python
import litsdb

db = litsdb.Database(path)

user_names = db.map("user_names")
user_names[user_id] = name

users = db.seq("users")
for u in users:
    ...
```

Available collections:
- `db.map`: key-value storage
- `db.seq`: ordered sequence
- `db.set`: set of unique values
- `db.multimap`: many-keys-to-many-values relations, e.g. likes (user ID -> video IDs)

Because values are stored via Pylits, lists, dicts, and other literal structures keep their types:

```python
items = db.map("items")

# key: 11sfrom 1 to 5 
# val: 4l1bT1s21i31d1i41i5
items["from 1 to 5"] = [True, "2", 3, {4: 5}] 
```

## A Programming Language from Scratch
I wrote a full object-oriented programming language, including every stage of the pipeline: a **lexer**, a **parser**, a **semantic analyzer**, and an **executor**.

**Source** *-(CharacterStream)->* **Lexer** *-(TokenStream)->* **Parser** *-(AST)->* **Analyzer** *-(Annotated AST)->* **Executor** *->* **Result**

It's interpreted, strongly typed, and imperative. It has classes and OOP, but you can write in other styles too. It also has closures and namespaces, and the syntax lands somewhere between Rust and Python.

It's a complete language, not a toy subset. Anything I wanted to write, I could run.

After building every stage myself, writing a lexer, parser, and executor feels routine now. I could do it with closed eyes.
