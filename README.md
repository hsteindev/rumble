# **Drone**

A php migration tool for AWS dynamoDB.

##### **Requirements**
To use ```drone``` for migration and seeding, you should either have aws dymanodb locally installed or have a valid aws credential for the remote version in a particular aws region.

##### **Disclosure**
```drone``` is far from being complete compared to other migration tool out there. More features will be added in the future.

##### **Naming Convention**
Since ```drone``` is still in infancy, it cannot generate migration or seeder file automatically. You have to manually create files for both migrations and seeds. Migration and Seed files are to be placed in the ```Migrations``` and ```Seeds``` directories. 
A migration and seed file must be name with and underscore ```(_)``` separating each word that makes up the file name. e.g:
```creat_app_records_table.php``` is a valid file name for a migration or a seed file. NB: The ```.php``` is required also.
While the file name uses underscore ```(_)``` style, the class name for the file uses Pascal style. i.e for the above file, the class name will be ```CreateAppRecordTable```. 

##### **Class Definition**
- Migration : Every migration file (class) must extend the base ```Migration``` class in the ```Drone``` directory and must implement a ```up``` method.
- Seed: Every seed file (class) must extend the base ```Seeder``` class in the ```Drone``` directory and must implement a ```seed``` method.

##### **Using Drone**
- **Migration:** to migrate files, run ```php drone.php migrate``` from terminal at ```/srv/workers``` directory
    e.g: ```/srv/workers php drone.php migrate```
- **Seed:** to seed files, run ```php drone.php seed``` from terminal at ```/srv/workers``` directory
    e.g: ```/srv/workers php drone.php migrate```

##### **Supported DynamoDb Features**
Currently, ```drone``` supports only the below dynamodb features;
- Creating tables 
- Updating tables
- Deleting tables
- Adding Item (also multiple with batch write)

##### **Tutorial - Create a new table**
The below code sample shows the minimum required params to create a dynamodb table using ```drone```. This is from the ```up``` method of the migration file (```create_app_records_table.php```).
The ```up``` method, is the only required method to be implemented by every migration file you create.
```php
<?php
include_once("Drone/Migration.php");

class CreateAppRecordsTable extends Migration 
{
    public funtion up()
    {
        $table = $this->table('app_records'); //table name
        $table->addAttribute('app_uid', 'S'); //primary key data type - String(S) 
        $table->addHash('app_uid');  //primary key 
        $table->setWCU(10); //Write Capacity Unit (Provisioned write throughPut) 
        $table->setRCU(10); //Read Capacity Unit (Provisioned read throughPut)
        $table->create();  //create table
    }
}
```

##### **Tutorial - Seed table**
The below sample code shows the minimum required params to seed a dynamodb table (sample created above). This is from the ```seed``` method of the seed file (```app_records_seeder.php```).
The ```seed``` method is the only required method to be implemented by every seed file you create

```php
<?php
include_once("Drone/Seeder.php");

class AppRecordsTableSeeder extends Seeder 
{
    public function seed()
    {
        $table = $this->table('app_records');
        $table->addItem(['app_uid' => 'x435-'.rand().'-u2fX'], ['install' => ['time' => time(), 'reason' => 'amazing app!']]); //add sample record(item) 
        $table->addItem(['app_uid' => '944-'.rand().'-y4hh4', 'events' => ['action' => 'click', 'date' => '2017-04-10']]); //add another
        $table->save();
    }
}

```