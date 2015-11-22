# reverse_geocoder
Tizen Naive Reverse Geocoder API Demo


Screen shots
------------

<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/create_empty_prj.png" alt="Create Empty Tizen Native Project" style="wi    dth:500px"/>

<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/set_privileges.png" alt="Set Privileges" style="wi    dth:500px"/>


Network Requiements
-------------------

Be sure your deveice/emulator have a valid internet connection.


Code snippets
-------------

### Add Maps Service Handle to app data

```
#include <maps_service.h>

typedef struct appdata {
	Evas_Object *win;
	Evas_Object *conform;
	Evas_Object *label;
	maps_service_h maps;
} appdata_s;
```

### Create Maps Serivice

```
/* Create Maps Service */
if(maps_service_create("HERE", &ad->maps) != MAPS_ERROR_NONE)
	return false;

/* Set Maps Provider Key */
maps_service_set_provider_key(ad->maps, "your-security-key");
```

### Release Maps Service

```
/* Release all resources. */
appdata_s *ad = data;
maps_service_destroy(ad->maps);
```


### Run Reverse Geocoder

```
/* Run Geocoder */
int request_id = 0;
maps_service_reverse_geocode(ad->maps, 55.7601365, 37.6164599, NULL,
		reverse_geocode_cb, ad, &request_id);
```

### Define Reverse Geocoder Callback

```
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

   // Release the results
   free(city);
   free(street);
   free(building_number);
   maps_address_destroy(address);
}
```


