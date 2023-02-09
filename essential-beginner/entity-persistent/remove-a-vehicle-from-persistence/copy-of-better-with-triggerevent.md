# Copy of (better) With TriggerEvent

{% hint style="info" %}
Client **need to write TriggerServerEvent**
{% endhint %}

### From server file

```lua
TriggerEvent('EP:LostVehicle', registery)
```

### From client file

```lua
TriggerServerEvent('EP:LostVehicle', registery)
```

## Details of arguments

{% tabs %}
{% tab title="registery" %}
{% hint style="warning" %}
This argument is mandatory.
{% endhint %}

Default registery is vehicle plate: GetVehicleNumberPlateText(vehicle)
{% endtab %}
{% endtabs %}



