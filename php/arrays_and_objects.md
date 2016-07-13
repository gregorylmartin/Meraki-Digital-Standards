#Data Arrays and Objects
[Back to General PHP Standards](https://github.com/gregorylmartin/Meraki-Digital-Standards/tree/master/php)

We do a tremendous amount of work with "data objects" (DataSets, RecordSets, Tables, ResultSets, DataArrays).  They go by lots of names, but they are basically object or array constructs holding the data we need to render.  For consistency here, let's just call them RecordSets.

By default, when coming from the database in our PHP/CodeIgniter setup, RecordSets will look something like this when rendered to the screen via the print_r() command...

```
Array (
  [0] => stdclass Object
    (
      [player] => Smith, Emmitt
      [jersey] => 22
    )
  [1] => stdclass Object
    (
      [player] => Aidman, Troy
      [jersey] => 8
    )
)
```
Basically, the RecordSet is an Array "of" Objects.  The overall RecordSet is an Array.  Each "row" within the RecordSet is an Object.

Because of this, we can easily work with the data.  A call to a function in one of our "model" files will generally return a RecordSet.
```php
$this->data['rptData'] = $this->mdl_api->getPlayers( $teamId );
```

To loop through a RecordSet, then, we do the following...
```php
foreach ( $this->data['rptData'] as $row ) {
  echo $row->player
}
```
Note: When passing $this->data to a view, the view does not need to refer to $this->data any longer.  So, in the "view", $this->data['rptData'] becomes $rptData.

Or, to get a bit more advanced...
```
<table>
  <?php foreach ( $rptData as $row ) { ?>
    <tr>
      <td><?php echo $row->player ?></td>
      <td><?php echo $row->jersey ?></td>
    </tr>
  <?php } ?>
</table>
```

Most of our use cases are fairly specific.  However, using the example above, it would also be very simple to add the header row to the table with the column names.  This would be done as follows...
```
<table>
  <?php if ( !empty( $rptData[0] ) ) {
    <tr>
      <?php foreach ( $rptData as $key => $value ) { ?>
        <td><?php echo $key ?></td>
      <? } ?>
    </tr>
    <?php foreach ( $rptData as $row ) { ?>
      <tr>
        <td><?php echo $row->player ?></td>
        <td><?php echo $row->jersey ?></td>
      </tr>
    <?php } ?>
  <?php } else { ?>
    <tr><td>No Data Found</td></tr>
  <?php } ?>
</table>
```
The key to that snippet above is...
```php
foreach ( $rptData as $key => $value )
```
