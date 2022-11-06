# (better) With TriggerEvent

{% hint style="info" %}
From server : **need to write TriggerClientEvent**.
{% endhint %}

```lua
TriggerEvent('EP:Persist', vehicle, registery, data)

TriggerClientEvent('EP:Persist', source, NetworkGetEntityFromNetworkId(vehicle), registery, data)
```

## Details of arguments

{% tabs %}
{% tab title="vehicle" %}
{% hint style="warning" %}
This argument is mandatory.
{% endhint %}

Vehicle args is your vehicle entity.
{% endtab %}

{% tab title="registery" %}
{% hint style="info" %}
Optional argument
{% endhint %}

REGISTERY serves as a unicated key to avoid duplications in the database. By default: vehicle plate (automatically managed by the script)
{% endtab %}

{% tab title="data" %}
{% hint style="info" %}
Optional argument
{% endhint %}

DATA serves as a table for adding data or modifying data when adding the vehicle to the database.\
By default: none.\
To be used as follows:

```lua
local data = {model = GetHashKey('adder'), license = "player_license"}
-- You need to add license row inside persistent_tables
```
{% endtab %}
{% endtabs %}



