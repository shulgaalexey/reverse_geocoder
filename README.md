# How to use Tizen Native Reverse Geocode API in 3 simple steps

*Since Tizen 2.4*

## What is Reverse Geocode?
**Reverse Geocode** translates the place geographical coordinates into its address.

Reverse Geocode API is one of the Maps Services provided by the Tizen Native Location Framework.

<img src="https://github.com/shulgaalexey/reverse_geocoder/blob/master/doc/maps_service.png" alt="Tizen Native Maps Service API" style="width:500px"/>

To start using the Reverse Geocode API we are going to:

1. Create an empty Tizen Native Application
2. Start Maps Service
3. Run Reverse Geocode

## Prerequisites
*The following assumes that you have already basic knowledge in Tizen development:* https://developer.tizen.org/development/getting-started/preface

Maps Service API requires a security key, issued by Maps Provider.

In case of HERE, the security key is a concatenation of app_id and app_code.

This is generated on https://developer.here.com/plans/api/consumer-mapping accordingly to your consumer plan.
```
“your-security-key” is “app_id/app_code”
```

Note: Be sure your deveice or emulator has a valid internet connection.

## 1. Create empty Tizen Native Application
In the IDE create an empty Application and run it on emulator or device.

<img src="https://github.com/shulgaalexey/reverse_geocoder/blob/master/doc/create_empty_prj.png" alt="Create Empty Tizen Native Project" style="width:500px"/>

When the application started, the "Hello Tizen" label should appear.

NOTE: Get familiar with instructions on how to create an empty application at https://developer.tizen.org/development/
Go to "Native Application" -> "Creating Your First Tizen Application".


## 2. Start Maps Service
Include the Maps Service API header file:

```C
#include <maps_service.h>
```

NOTE: This allows using all Native Maps Service API. For more details visit https://developer.tizen.org/development/api-references/.
Navigate to 2.4 API References -> Native Application ->Mobile Native -> Native API Reference -> Location -> Maps Service.

Add a Maps Service handle to the appdata_s structure:

```C
typedef struct appdata {
	Evas_Object *win;
	Evas_Object *conform;
	Evas_Object *label;
	maps_service_h maps; /* Handle of Maps Service */
} appdata_s;
```

Create an instance of Maps Service API in the app_create() function:

```C
static bool
app_create(void *data)
{
	appdata_s *ad = data;
	create_base_gui(ad);

	/* Specify Maps Provider name. */
	if(maps_service_create("HERE", &ad->maps) != MAPS_ERROR_NONE)
		return false;

	/* Set security key, issued by Maps Provider */
	maps_service_set_provider_key(ad->maps, "your-security-key");

	return true;
}
```

Release The Maps Service in app_terminate() function:

```C
static void
app_terminate(void *data)
{
	/* Release all resources. */
	appdata_s *ad = data;
	maps_service_destroy(ad->maps);
}
```

Add privileges, required for Maps Service API:

 * mapservice
 * internet
 * network.get


<img src="https://github.com/shulgaalexey/reverse_geocoder/blob/master/doc/set_privileges.png" alt="Set Privileges" style="width:500px"/>



## 3. Run Reverse Geocode
Add Reverse Geocode request into the app_create() function:

```C
/* Use Reverse Geocode API */
int request_id = 0;
maps_service_reverse_geocode(ad->maps, 55.7601365, 37.6164599, NULL, reverse_geocode_cb, ad, &request_id);
```

Implement Reverse Geocode callback:

```C
static void
reverse_geocode_cb(maps_error_e result, int request_id, int index, int total,
		maps_address_h address, void* user_data)
{
    char *city = NULL;
    char *street = NULL;
    char *building_number = NULL;

    maps_address_get_city(address, &city);
    maps_address_get_street(address, &street);
    maps_address_get_building_number(address, &building_number);

    char reverse_geocode[0x80] = {0};
    snprintf(reverse_geocode, 0x80, "Reverse Geocode is: %s, %s, %s",
		   building_number, street, city);

    appdata_s *ad = user_data;
    elm_object_text_set(ad->label, reverse_geocode);

    /* Release the results */
    free(city);
    free(street);
    free(building_number);
    maps_address_destroy(address);
}
```

Run the Application.

At first we will see the familiar "Hello Tizen" line, but in a couple of moments it should change to "Reverse Geocode is: 6, ulitsa Bol'shaya Dmitrovka, Moscow”.
This would indicate the reverse geocode of the geographical coordinates (55.7601365, 37.6164599).



## Reference
https://developer.tizen.org/development/tutorials/native-application/location/maps-service#geocode

https://developer.tizen.org/community/tip-tech/how-use-tizen-native-geocode-api-3-simple-steps
