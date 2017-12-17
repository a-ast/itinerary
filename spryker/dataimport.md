Keyword research:

Spryker (70 searches; rank 6)
Spryker Commerce (10 searches; rank 2)
Spryker tutorial (0 searches; rank 8)

# A guide to using the Spryker DataImport module  

**In an [earlier post](https://inviqa.com/blog/spryker-vs-magento)
we explored some of the similarities and differences between 
[Spryker Commerc**e](https://inviqa.com/technologies/spryker) and
[Magent**o](https://inviqa.com/technologies/magento-ecommerce),
 one of the world’s most popular ecommerce platforms. 
 Here, in our first Spryker tutorial, Andrey Astakhov talks us through 
 the Spryker DataImport module.**

## Simplifying data migration to Spryker

The attraction of [moving to a commerce framework](https://inviqa.com/blog/ensuring-business-value-ecommerce-platform-migration) 
like Spryker lies in the ability to innovate quickly without having to tear-up 
your existing systems. But, as with any ecommerce platform move, 
migrating data to your new Spryker platform can be time-consuming and complex.

Thankfully, Spryker has a module that’s designed to simplify the process. The DataImport module allows you to upload data from existing external sources – such as CSV files or a product information management (PIM) system – to your online store. Like all other Spryker modules, the module follows the principles of package design and communicates with other modules via internal APIs. It uses a console command as its entry point for importing data from CSV files to the database.  

The DataImport module has been invaluable in helping our clients, such as German office supplies company [Certeo](https://inviqa.de/kunden/certeo), to import data to their new Spryker stores. Like any other ecommerce tool, it has its pros and cons, although most of the cons can be addressed by making tweaks to your project code.

In this tutorial, I’ll explain the concepts and structure of the DataImport module, and show you how to use and extend it in your ecommerce projects.

## Installing Spryker DataImport

If your web shop is based on Demoshop, then you already have an installed DataImport module. 

If not, run this command:

```bash
composer require spryker/data-import
```

## Module classes and interfaces

All projects are different. For this reason Spryker DataImport module provides a very generic solution that allows you to customise data imports in your project code.

The main building blocks of DataImport module are as follows:

* Data reader
* Import step
* Step broker
* Import hooks

The core of the module is the DataImporter class. The idea behind DataImporter is to retrieve 
import data using a data reader and process data with steps that are combined in 
brokers. Hooks allow you to apply additional operations before and / or after import.

### Data reader

The DataImport module comes with `CsvReader` class. 
If your data is not stored in CSV format, it’s a good idea to implement 
a data reader in your project, which provides a way to sequentially 
get data from a data source. Later on I’ll give an example of a reader for XML files.

The following diagram shows two important interfaces: 
`DataReaderInterface` and `ConfigurableDataReaderInterface`.

![image alt text](image_0.png)

Now let’s take a look at the purpose of each interface:

<table>
  <tr>
    <td>Interface</td>
    <td>Purpose</td>
  </tr>
  <tr>
    <td>DataReaderInterface</td>
    <td>Allows you to read data from the data source.</td>
  </tr>
  <tr>
    <td>ConfigurableDataReaderInterface</td>
    <td>Allows you to configure data reader with an instance of DataImporterReaderConfigurationTransfer.</td>
  </tr>
</table>


### Data steps

Data step is an operation on a single entry of imported data. 
Inserting or updating product data into database is a typical example 
of a data step.

As you can see here, the Spryker DataImport module provides you with three interfaces:

![image alt text](image_1.png)

Again, let’s explore the respective purposes of those interfaces:

<table>
  <tr>
    <td>Interface</td>
    <td>Purpose</td>
  </tr>
  <tr>
    <td>DataImportStepInterface</td>
    <td>Executes an operation on data
Examples:
Persist (store) product information to product tables
Store category data to database</td>
  </tr>
  <tr>
    <td>DataImportStepBeforeExecuteInterface</td>
    <td>Performs operations before processing a line of imported data</td>
  </tr>
  <tr>
    <td>DataImportStepAfterExecuteInterface</td>
    <td>Performs operations after processing a line of imported data
Examples:
Touch updated entities</td>
  </tr>
</table>


In your project you’ll probably implement many data steps. The most important steps will be those that write data to a database. By convention they are named Writers.

#### TouchAwareStep

The DataImport module contains one implementation of a step. 

`TouchAwareStep` is a helper class that can be used in your project 
to simplify a communication with the Touch module. 
The main idea of this step is to send identifiers of modified entities to 
TouchFacade using bulk update.  

#### Demoshop data steps

Demoshop is an example of an ecommerce application based on the Spryker framework. 
Take a look at how it implements data steps and use it as inspiration for your 
project.

In the Demoshop you can find several data steps:

<table>
  <tr>
    <td>Class</td>
    <td>Purpose</td>
  </tr>
  <tr>
    <td>AddLocalesStep</td>
    <td>Fetches locales from the database and adds their identifiers and names to a processed dataset. It allows other data steps to reuse locale information without querying database</td>
  </tr>
  <tr>
    <td>LocalizedAttributesExtractorStep</td>
    <td>Groups attributes from a dataset by locale</td>
  </tr>
</table>


Since data steps may get data from a database or perform other time- 
and computer-resource-intensive operations, it makes sense to fetch 
some data only once and cache it for later reuse.

One of the possible ways to cache data is to store it directly to a dataset. 
As you can see in `AddLocalesStep`, it fetches locales for 
the very first record from a data reader and saves locales to the dataset:

```php
public function execute(DataSetInterface $dataSet)
{
   if (empty($this->locales)) {
       $localeEntityCollection = SpyLocaleQuery::create()
           ->filterByLocaleName($this->availableLocales, Criteria::IN)
           ->find();

       foreach ($localeEntityCollection as $localeEntity) {
           $this->locales[$localeEntity->getLocaleName()] = $localeEntity->getIdLocale();
       }
   }

   $dataSet[static::KEY_LOCALES] = $this->locales;
}
```

### Data step broker

Data step broker is nothing more than a collection of data steps with an `execute` method that calls all embedded data steps:

![image alt text](image_2.png)

In this way you can create a new step broker as detailed below:

$dataSetStepBroker = new DataSetStepBroker();

```php
$dataSetStepBroker
   ->addStep(new AddLocalesStep())
   ->addStep(new LocalizedAttributesExtractorStep([]));
```

### DataSetStepBrokerTransactionAware

`DataSetStepBrokerTransactionAware` wraps data modifications in transactions. 
This data step broker opens transactions on the beginning and commits portions 
of data. 

### Data import hooks

Data import hook is a data operation that the Spryker DataImporter executes 
before or after an import.

In the Spryker core DataImport module you’ll find two interfaces:

![image alt text](image_3.png)

<table>
  <tr>
    <td>Interface</td>
    <td>Purpose</td>
  </tr>
  <tr>
    <td>DataImporterBeforeImportInterface</td>
    <td>Runs before data import</td>
  </tr>
  <tr>
    <td>DataImporterAfterImportInterface</td>
    <td>Runs after data import
Example: call touch engine for modified data after import</td>
  </tr>
</table>

| aaa | fff |
| aaa | fff |
|


## How DataImporter runs data steps and hooks 

At the beginning of a data import, DataImporter iterates before-import data hooks, then goes through the available data step brokers and finalises the import by running after-import hooks:

![image alt text](image_4.png)

![image alt text](image_5.png)

*Above: a simplified class diagram of the DataImport module*

Using DataImport in a project

DataImport is a tiny framework that gives you basic functionality to implement data import in your project. 

**Create a simple data importer**

The following steps explain how to create and register a straightforward importer with a single data step.

The first thing to do in your project is to create a data step class inside a `Pyz\Zed\DataImport\Business\Model` namespace and implement DataImportStepInterface.

[[code block]](https://gist.github.com/a-ast/e1aaae92c089f5be8c157927296c9858#file-yourwriterstep-php)

Additionally, you can implement DataImportStepBeforeExecuteInterface and / or DataImportStepAfterExecuteInterface in the very same or in a different class.

Now that you have a data step, create a new importer and a data step broker in your factory:

[[code block]](https://gist.github.com/a-ast/e1aaae92c089f5be8c157927296c9858#file-dataimportbusinessfactory-php)

Last, but not least, register you new data importer in a collection of importers:

[[code block]](https://gist.github.com/a-ast/e1aaae92c089f5be8c157927296c9858#file-dataimportbusinessfactory-getimporter-php)

 

**How to add new data reader**

If your data is not in CSV files, but has another format such as XML files, implement your XmlReader as shown in this example:

[[code block]](https://gist.github.com/a-ast/da432527d7dbae7c3cf6b13f624582f5#file-xmldatareader-php)

And then change how the data reader is created in a factory:

[[code block]](https://gist.github.com/a-ast/da432527d7dbae7c3cf6b13f624582f5#file-dataimportbusinessfactory-php)

**How to output import progress to console**

If your data import takes longer than a couple of seconds and you need to visualise how the import process is progressing, you’ll need to print some intermediate result in the meantime.

One of the options for doing this is to log messages and show them in the console output. 

The Spryker Log module, which uses the well-known Monolog library, is your starting point to start logging.

First, create a LoggerConfig class that implements `LoggerConfigInterface` from Log module.

[[code block] ](https://gist.github.com/a-ast/87c742641391188221d366b58195077d#file-loggerconfig-php)

Then use LoggerTrait to output message from data import step:

[[code block] ](https://gist.github.com/a-ast/87c742641391188221d366b58195077d#file-yourwriterstep-php)

You might need to change the standard Monolog output format to print pretty messages with timestamps:

[[code block]](https://gist.github.com/a-ast/87c742641391188221d366b58195077d#file-loggerconfig-lineformatter-php)  

How to use the DataImport console command

The DataImport module provides a console command to execute import:

vendor/bin/console data:import

By default, it runs every registered importer, even if some of the steps fail. As a result, you can see the following output at the end, without knowing the exact cause of the failure:

![image alt text](image_6.png)

 

To discover what’s caused an import to fail you’ll need to run 
the import console command with `-t` option:

```bash
vendor/bin/console data:import -t
```

Enabling this option will stop the data import immediately and will print 
an error message with full trace information.

Apart from that, you can use the console command to import a particular 
data subset using offset and limit. This command will import the first 
ten categories:

`vendor/bin/console data:import:category -o 1 -l 10`

## Suggestions for improving DataImport

The following three points address some issues I encountered working with the DataImport module, along with some suggestions for how Spryker developers can improve the module going forward:

### 1. Batch processing

One of the biggest issues with DataImport is the fact that it does not allow you to work with batches of import data; data steps can only access a single data item despite the fact that batch operations are more effective, especially If you work with a huge amount of data. 

In my opinion, developers needs something like `DataImportStepInterface::executeForCollection` with a configurable size of collection passed as an argument of this method. 

### 2. Communicating import progress

Another challenge is that the Spryker DataImport module does not provide a simple way to communicate with a console.  Demoshop import console command does not show any progress information and prints import results at the end of execution.

We’ve already discussed one possible solution for writing to console using logger. Another possible option is to use EventDispatcher.

### 3. More examples for other import data sources

DataImport is so csv-oriented that it seems to be designed mostly for import from CSV files. You can see it in `DataImporterReaderConfigurationTransfer` where almost all fields relate to the specific file format: csvDelimiter, csvEnclosure, etc.  

## Wrapping up

As we’ve explored in this guide, the DataImport module is a useful way to simplify 
the process of importing data to your Sryker online shop. 
Due to an architecture that follows SOLID principles, 
it’s possible to configure a data import from any datasource, 
either from static files, or a PIM solution such as [Akeneo](https://inviqa.com/technologies/akeneo). 

For these reasons the Spryker DataImport module is flexible, 
capable of meeting the requirements of nearly every Spryker project, 
and is well worth checking out.

*Proven to perform in the most complex and demanding environments, 
[Spryker Commerce](https://inviqa.com/technologies/spryker) 
separates frontend apps and backend capabilities, so that retailers, 
multi-channel players, or marketplaces can very quickly adapt to changing 
consumer behaviours and markets. 
[Talk to an ecommerce consultant today](https://inviqa.com/contact) to explore 
if Spryker could be right for you.*

Related reading 

* [How to add a new DataImport Type in Spryker](https://academy.spryker.com/enablement/howtos/ht_data_import.html)
* [Spryker vs Magento](https://inviqa.com/blog/spryker-vs-magento)
* [Learn more about Inviqa’s Spryker Commerce services](https://inviqa.com/technologies/spryker)

