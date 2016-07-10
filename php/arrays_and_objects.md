#Data Arrays and Objects
[Back to General PHP Standards](https://github.com/gregorylmartin/Meraki-Digital-Standards/tree/master/php)

We do a tremendous amount of work with "data objects" (DataSets, RecordSets, Tables, ResultSets, DataArrays).  They go by lots of names, but they are basically object or array constructs holding the data we need to render.  For consistency here, let's just call them RecordSets.

By default, when coming from the database in our PHP/CodeIgniter setup, RecordSets will look something like this...

```
Array (
  [0] => stdclass Object
    (
      [Player] => Smith, Emmitt
      [Jersey] => 22
    )
  [1] => stdclass Object
    (
      [Player] => Aidman, Troy
      [Jersey] => 8
    )
)
```
