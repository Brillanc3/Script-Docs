# Copy of (better) With TriggerEvent

{% hint style="info" %}
Client / Server is exactly same, **no need to write TriggerServerEvent or TriggerClientEvent**.
{% endhint %}

### From server file

```lua
TriggerEvent('EP:LostVehicle', vehicle)
or
TriggerEvent('EP:Server:LostVehicle', netId)
```

### From client file

```lua
TriggerEvent('EP:LostVehicle', vehicle)
or
TriggerServerEvent('EP:Server:LostVehicle', netID)
```

## Details of arguments

{% tabs %}
{% tab title="vehicle" %}
{% hint style="warning" %}
This argument is mandatory.
{% endhint %}

Vehicle args is your vehicle entity.
{% endtab %}

{% tab title="netID" %}
{% hint style="warning" %}
This argument is mandatory.
{% endhint %}

netID can be obtien with&#x20;

<pre class="language-lua"><code class="lang-lua">-- return integer value if DoesEntityExist(vehicle)
<strong>NetworkGetNetworkIdFromEntity(vehicle)</strong></code></pre>
{% endtab %}
{% endtabs %}



