#Data Arrays and Objects
[Back to General PHP Standards](https://github.com/gregorylmartin/Meraki-Digital-Standards/tree/master/php)

We do a tremendous amount of work with "data objects" (DataSets, RecordSets, Tables, ResultSets, DataArrays).  They go by lots of names, but they are basically object or array constructs holding the data we need to render.  For consistency here, let's just call them RecordSets.

By default, when coming from the database in our PHP/CodeIgniter setup, RecordSets will look something like this when rendered to the screen via the print_r() command...

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
Basically, the RecordSet is an Array "of" Objects.  The overall RecordSet is an Array.  Each "row" within the RecordSet is an Object.

Because of this, we can easily work with the data.  A call to a function in one of our "model" files will generally return a RecordSet.
```php
$this->data['rpt_data'] = $this->mdl_api->get_players( $team_id );
```

To loop through a RecordSet, then, we do the following...
```php
foreach ( $this->data['rpt_data'] as $row ) {
  echo $row->Player
}
```
Note: When passing $this->data to a view, the view does not need to refer to $this->data any longer.  So, in the "view", $this->data['rpt_data'] becomes $rpt_data.

Or, to get a bit more advanced...
```
<table>
  <?php foreach ( $this->data['rpt_data'] as $row ) { ?>
    <tr>
      <td><?php echo $row->Player ?></td>
      <td><?php echo $row->Jersey ?></td>
    </tr>
  <?php } ?>
</table>
```
