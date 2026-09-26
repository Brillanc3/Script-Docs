---
description: Texts, command names, adding a language
icon: language
---

# Languages

```lua
-- config/shared.lua
Config.Locale = 'en'
```

| File             | Commands               |
| ---------------- | ---------------------- |
| `locales/en.lua` | `/tarp`, `/untarp`     |
| `locales/fr.lua` | `/bacher`, `/debacher` |

Each file contains **every player text and the command names** (`command_cover`, `command_uncover`), plus the staff panel texts (`panel_*`). A key missing from the selected language falls back to `en`.

## Add a language

{% stepper %}
{% step %}
Copy `locales/en.lua` to `locales/de.lua` (for example).
{% endstep %}

{% step %}
Replace `Config.Locales.en` with `Config.Locales.de`.
{% endstep %}

{% step %}
Translate the texts. Keep `%s` and `%d`: they are replaced by values (tarp id, minutes).
{% endstep %}

{% step %}
Set `Config.Locale = 'de'`, then `refresh` and `restart vehicle_tarp`.
{% endstep %}
{% endstepper %}

{% hint style="warning" %}
Changing the command names (or the language) resets the keys players bound to them.
{% endhint %}
