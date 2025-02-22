# wix-sync

![license MIT](https://img.shields.io/github/license/yoavaa/velo-sync)
![build for Velo by Wix](https://img.shields.io/badge/Built%20for-Velo%20by%20Wix-blue)

The `Wix-sync` tool is used to sync data into a Wix Data / Content Manager website. 
The tool syncs database items and media files (images, videos, audio files or documents)
making them available in a wix data collection.

Wix Sync supports a few modes of operations

| command | data | description |
|------|------|-----|
| import | without `keyField` | each data item is a new item in the collection |
| import | with a `keyField` | consolidates new items with the items in the collection based on the `_id` field |
| sync | without `keyField` | like import without `keyField`, but also removes all other items from the collection |
| sync | with a `keyField` | like import with an `keyField`, but also removes all other items from the collection |

The velo-sync application can be used in two different ways

* Used as a **CLI Application** to import and sync `CSV` files and referenced media
* Used as an **API** to import javascript objects and referenced media

# Usage as a CLI Application

To use the velo sync CLI Application, you need to prepare your data, prepare your site and prepare the app. 

## Prepare your Data

### 1. Prepare the CSV File

The data to be imported or synced should be formatted as a standard CSV file, with the following conventions
* header line with field names.
* any field that includes a comma (`,`) or new like should be quoted with `"`.
* field values that include quotes should escape the quotes with double quotes `""`.

An example CSV file

```
Image,Artist,Location,Description,ID,Name
wix:image://v1/596320_525e18eaa2ce459a94dd3b812a7db930~mv2.jpg/BenWilson_chewing_gum_issa_23.jpg#originWidth=634&originHeight=436,c1c03e11-024b-4dad-a689-1c3610913b9b,London Bridge,,0451962b-9cd2-4fe2-aa24-f7973d073d75,Little Mermaid
wix:image://v1/fc7570_846eb609b9d640d184345e6309f8768c~mv2.jpg/%20Shop%20till%20you%20drop%20%20%20.JPG#originWidth=1600&originHeight=1067,50072d76-8368-42f7-99ee-0e9296029d75, Bruton Street,"Also known as the “Falling Shopper”, the mural is located on Bruton Lane on the side of a large office building in the heart of the West End district. The piece is over two storeys up and represents a woman falling with a trolley from the top of a building. Banksy’s aim was to point out the dangers of consumerism.  The mural was painted in November 2011 in broad daylight. A scaffolding and a tarpaulin were used to make sure nobody caught the artist red-handed. “Shop Till You Drop” is still visible and quite damaged.",0560c783-f374-44f6-b318-908d6de77816, Shop Till You Drop
wix:image://v1/596320_f0681085ac544bea9527272059955ba2~mv2.jpg/46600548_595929587504016_290259145196053.jpg#originWidth=1080&originHeight=1080,777a0f48-b2ff-421e-a435-739081cf05ba,King John Court,,0a910c5e-cf3c-4d92-b911-8db7819296f1,Artemis
wix:image://v1/596320_65dfa944076d44a9a945868d7702331b~mv2.jpeg/IMG_6611.jpeg#originWidth=1000&originHeight=750,59430c6f-11f4-49c5-a7f5-19b855d2b5f3,Shoreditch Underground,,0b75e1d7-073c-44ae-b965-a1fa6bb2c15a,Hey Baby
wix:image://v1/fc7570_dfbf8d18bf2f4d8db98a48b65e491d78~mv2.jpg/Banksy-There-is-Always-Hope.jpg#originWidth=1500&originHeight=1076,50072d76-8368-42f7-99ee-0e9296029d75,South Bank,"Arguably Banksy’s most iconic piece, it appeared in South Bank, London in around 2002. The words ‘There Is Always Hope’ are written just behind a young girl, who can be seen reaching for a balloon in the shape of a heart. Intense debate has raged on over the years regarding the true meaning of this stencil, with a variety of ideas involving love, innocence and – obviously – hope",1c138979-d640-4d53-b390-187a4196db5f,There Is Always Hope
wix:image://v1/596320_a2535724be2c4376b382260f10d7f069~mv2.jpg/BaltimoreBorn.jpg#originWidth=1080&originHeight=827,777a0f48-b2ff-421e-a435-739081cf05ba,Old Street,,297516b7-3389-49ae-b1c7-14a28b4ba76b,Baltimore Born
wix:image://v1/fc7570_221a6ef024794bb69a43a208225643bd~mv2.jpg/street-art-in-london-banksy-graffiti-pai.jpg#originWidth=640&originHeight=428,50072d76-8368-42f7-99ee-0e9296029d75,Portobello Road Market," This piece can be found beside Regent’s Canal tunnel and under the London Transport Police Headquarters in Camden. The mural, painted in 2009, represents a city worker covering the work of another graffiti artist painted in 1985 with grey wallpaper.  This wall became the battleground of a graffiti war between Banksy and the deceased London graffiti legend King Robbo. Shortly after, Robbo struck back and covered Banksy’s piece on the pretext that he broke the underground code of conduct. The wall undergone eight other modifications from both artists. The feud stopped when King Robbo was hospitalised for life-threatening head injuries. When Banksy heard about King Robbo’s serious health condition, he painted an ode to Robbo’s original graffiti piece. As a tribute to Robbo, the mural was restored to its original form with slight changes.",414c339e-0300-44b9-b8a4-376d8f0acbca, Graffiti Painter
wix:image://v1/596320_057219a2167d4b6dbdac4ffc8bf70012~mv2.jpg/G.%20Davis%20is%20Innocent%20OK.jpg#originWidth=1200&originHeight=800,777a0f48-b2ff-421e-a435-739081cf05ba,Sclater Street,,63d5d8d0-3939-4c68-b641-8e93e7bc61f5,G. Davis is Innocent OK
wix:image://v1/fc7570_d259e3c970094cb3a2cdec0c8e28eb93~mv2.png/Screenshot%20at%20Mar%2005%2011-13-56.png#originWidth=1702&originHeight=1176,50072d76-8368-42f7-99ee-0e9296029d75,Rivington Street Shoreditch,"Banksy’s mural is located on Essex Road, North London. Painted in 2008 on the side of a pharmacy, Banksy’s work depicts a group of three children pledging their allegiance to the British supermarket chain Tesco.  One of them is raising a flag composed by a Tesco carrier bag. Even though it was quickly covered in perspex, “Very Little Help” has been vandalised on several occasions and it is currently partially damaged. The flag has been painted over and replaced with a tag from Robbo.",696f7b91-cd61-4a6c-a5f8-06fe1e916354,Tesco Kids
wix:image://v1/596320_e970850a978147cf89d49d62e0442736~mv2.jpg/ad2dd_Jose-Mendez-1.jpg#originWidth=470&originHeight=470,59430c6f-11f4-49c5-a7f5-19b855d2b5f3,Shoreditch,,6d779be3-bc57-49e0-b1a7-291d9429bbf8,Happy Walk
wix:image://v1/596320_25360281cc7e491bb4870bd5d32e909f~mv2.jpg/Garden+Of+Desire6+WEB.jpg#originWidth=709&originHeight=653,59430c6f-11f4-49c5-a7f5-19b855d2b5f3,Shoreditch,,7242d0da-73b7-431e-abaa-7efc18fa56f0,Garden of Desire
wix:image://v1/596320_c29d9c5caa03466ebe86d216f0c652f1~mv2.jpg/ben-wilson8_orig.jpg#originWidth=796&originHeight=800,c1c03e11-024b-4dad-a689-1c3610913b9b,London Bridge,,7bd23aac-2bce-4a69-8907-69152838ba82,London Sky
wix:image://v1/596320_1f2d833d379047579240f9f0e9c1ddad~mv2.jpg/1.jpg#originWidth=1000&originHeight=1000,59430c6f-11f4-49c5-a7f5-19b855d2b5f3,Shoreditch,,aed627a9-47da-404d-96ee-42e042eb6602,Red Gallery Window
wix:image://v1/596320_e8f7ee11aaa9428bb25233cf1304cb54~mv2.jpg/fingercrossed.jpg#originWidth=640&originHeight=640,777a0f48-b2ff-421e-a435-739081cf05ba,Brick Lane,,c1ba82f3-fd79-4860-87ce-d894c15c98fb,Fingers Crossed
wix:image://v1/596320_44bc2bdb102a48f3ae6e596bb2d2806c~mv2.jpg/tn_IMG_5069.jpg#originWidth=555&originHeight=740,c1c03e11-024b-4dad-a689-1c3610913b9b,London Bridge,,c2e3c665-664b-4797-a91f-f59559497ad2,I'll Quit Tomorrow
wix:image://v1/596320_1e8978648f0e4e56b79712f03ff80fa7~mv2.jpg/tn_IMG_0369-001.jpg#originWidth=864&originHeight=837,c1c03e11-024b-4dad-a689-1c3610913b9b,Old Street,,d5e474f2-7faf-47be-9702-27cbab81d0e7,BW Face
```

> note: more examples of CSV files can be found in the [example-datasets](./example-datasets) folder.

### 2. Prepare the schema File
                                              
The schema file describes the fields in the CSV file. It is important for parsing the fields from CSV to the proper types 
in the collection and to trigger the uploading of files and media into Wix and the collection.

An example schema file:

```json
{
  "keyField": "ID",
  "fields": {
    "Image": "Image",
    "Artist": "Reference",
    "Location": "string",
    "Description": "string",
    "ID": "string",
    "Name": "string"
  }
}
```

The schema file has two main elements
* keyField - [optional] the name of a column in the CSV file to use as the identifier of the line. This field
is used to correlate data in the collection with the imported data from the CSV file. In case the collection has an item with 
an `_id` that equals the `keyField`, the item will be updated. If there is no match, a new item will be added to the collection.  
* fields - the fields in the CSV file and how to parse / import each field. The fields element is an object, who's property names 
  (or keys) are column names in the CSV and the values are the collection types.
  
### Supported Types
               
|Type  | Valid Values | Example Values |
|------|------| --- |
| `string` | any text <br> texts with commas have to be quoted with "". <br> multiline text supported when quoted | some text <br> "text with comma, has to be quoted" |
| `number` | any valid javascript number | 1234 <br> 1234.34 <br> 1234.34e12 |
| `boolean` | true, false <br> yes, no <br> y, n |
| `Image` | relative file location <br> image url <br> wix image url| ./images/carson-arias-7Z03R1wOdmI-unsplash.jpg <br> https://some-domain.com/image.jpeg <br> `wix:image://v1/e15137_e05ba45c616448a0a0e9d73e726a9168~mv2.jpg/carson-arias-7Z03R1wOdmI-unsplash.jpg#originWidth=2598&originHeight=3247`|
| `Datetime` | any date time value parsable by `new Date(value)` | 2001-01-01T01:23:45.678Z <br> 2001-01-01 |
| `Time` | hours : minutes : seconds | 01:23:45 |
| `RichText` | HTML string that conforms to wix text element HTML. See [Text Element](https://www.wix.com/velo/reference/$w/text/introduction) | &lt;h1&gt;some text&lt;/h1&gt; |
| `Reference` | the `_id` of the referenced item | 0065e20a-48ca-4357-a8d6-e1ffa1d091bc |
| `URL` | Any valid url | https://some-domain.com/directory | 
| `Document` | relative file location <br> document url | ./document/sample.pdf <br> https://some-domain.com/doc.pdf |
| `Video` | relative file location <br> document video | ./video/production ID_3755072.mp4 <br> https://some-domain.com/vid.mp4 |
| `Audio` | relative file location <br> document audio | ./song.mp3 <br> https://some-domain.com/audio.mp3 |
| `Address` | The Wix address object, as described in the [Address Input value property](https://www.wix.com/velo/reference/$w/addressinput/value)| `{"formatted": "15 Fairmont Pl, Sterling, VA 20165, USA"}` <br>`{"city": "Sterling", "location": { "latitude": 39.0501381, "longitude": -77.426225 }, "streetAddress": { "number": "15", "name": "Fairmont Place", "apt": "" }, "formatted": "15 Fairmont Pl, Sterling, VA 20165, USA", "country": "US", "postalCode": "20165-5769", "subdivision": "VA" }` | 
| `Tags` | an array of strings | `["one", "two", "three"]` |
| `Array` | a javascript array | `[1,2,3]` |
| `Object` | a javascript object | `{a: 1, b: 2}` |
| `Gallery` | a comma separated list of URLs or files, or a gallery object as described in the [Gallery items property](https://www.wix.com/velo/reference/$w/gallery/items) | ./file1.jpg, ./file2.mp4, http://domain.com/image.png <br> `[{type: 'image', src: './file1.jpg'}, {type: 'video', src: './file2.mp4'}, {type: 'image', src: 'http://domain.com/image.png'}]`|

For media and files, the upload process supports 4 different formats of file references
* relative file location - the file will be uploaded to wix and replaced with a wix url (`wix:image`, etc.). 
  The file location is relative to the CSV file. 
* file url - the file will be uploaded to wix and replaced with a wix url
* wix statics url - public url of a file already stored on wix - the file will not be uploaded again, and will be replaced with a wix url
* wix url (`wix:image`, etc.) - the file will not be uploaded as it is already on wix.

## prepare your site

To enable velo sync on a velo site, one need to copy the [http-functions.js](./velo/backend/http-functions.js) into the backend folder in velo.
Then, a secret should be defined in Secrets Manager (under Settings / Secrets Manager) and define a secret with the name `velo-sync`.

1. copy the [http-functions.js](./velo/backend/http-functions.js) to the velo backend folder
2. define a secret `velo-sync` in secret manager, and give it a unique value
3. publish the site so that the http-functions file will be deployed to the site

## prepare the application
             
Make sure you have node.js installed, of version v12 and above. 
From the command line, run the application as below - you should see the app usage 

```
➜ npx velo-sync
npx: installed 115 in 8.748s
Usage: 
 npx velo-sync [command] [options...]

Commands:
  init          generates a config file for the import / export / sync process
  is-alive      tests the config and the connection to the site
  sync          runs the sync process
  import        runs an import process
```
                                                                                  
### 1. init the connection to the velo site

run `npx velo-sync init` and follow the instructions - enter the homepage url of the site and the secret from the secret manager.

```
➜ npx velo-sync init
npx: installed 115 in 7.133s
hello to velo-sync init
what is the url of the site homepage? https://dommain.com
what is the velo-sync secret? <your secret>
```

the init command creates a `config.json` file with the connection to the site 

### 2. test the connection

the `is-alive` command is used to verify the connection to the site, using the `config.json` file generated by the init command. 

```
➜ npx velo-sync is-alive
npx: installed 115 in 7.64s
checking if the API for site https://domain.com is alive...
API of site https://domain.com is working and alive!!!
```

### 3. run the sync process in dry run mode

Optional - run the `sync` or `import` commands in dry-run mode.
In dry-run mode, no data is imported or updated on the velo site, yet the
sync process reads all the data, all the referenced files and reports any potential issue.

Dry-run will detect any data parsing issues, or any file reference issue.

The `sync` and `import` commands require the following parameters
* `-f` - the CSV file to import
* `-c` - the name of the collection to import data into
* `-s` - the schema file
* `--dry` - run in dry-run mode.

### 4. run the sync process

You can now run the `sync` or `import` commands. 
`import` will import the data and files, while `sync` will also remove any other old items from the collection.
   
The `sync` and `import` commands require the following parameters
* `-f` - the CSV file to import
* `-c` - the name of the collection to import data into
* `-s` - the schema file

Example run

```
➜ npx velo-sync import -f Art.csv -c Items -s Art-schema.json
npx: installed 115 in 7.349s
0:0:0    starting import Art.csv to Items
0:0:0      check update state batch 0 with 16 items
0:0:3        check update state batch 0 with 16 items. ok: 13, need update: 0, not found: 3
0:0:3      upload images for batch 0 with 3 items needing image upload
0:0:3        uploaded images for batch 0 with 3 items. Uploaded Images: 0, rejected: 0
0:0:3      saving batch 0 with 3 items
0:0:3        saving batch 0 with 3 items. inserted: 3, updated: 0, skipped: 0, errors: []
0:0:3    statistics
0:0:3      read csv: 16
0:0:3      batches: 1
0:0:3      batched items: 16
0:0:3      check update state: 16
0:0:3      up to date items: 13
0:0:3      update: 3
0:0:3    completed importing Art.csv to Items
```
                
# Usage as an API

The wix-sync can be used directly as an API. To use it as an API, import the `createDataSync` function 
from the `npm` package.

```typescript
import {createDataSync, LoggingStatistics, LoggerRejectsReporter} from 'velo-sync';

async function run() {
  let data = [...]; // some data to be imported to Velo  
  let collection = 'items'; // the collection name
  let config = { // your connection config
    "siteUrl": "https://domain.com",
    "secret": "<your secret>"
  };
  let schema = { // your data schema
    "keyField": "ID",
    "fields": {
      "Image": "Image",
      "Artist": "Reference",
      "Location": "string",
      "Description": "string",
      "ID": "string",
      "Name": "string"
    }
  };
  let stats = new LoggingStatistics(); // statistics reporter
  let rejectsReporter = new LoggerRejectsReporter(stats); // rejects reporter
  let filesFolder = './'; // base folder for resolving relative filenames
  let uploadFilesCacheFile = '.upload-cache.db' // name of the file that stores names and hash of uploaded files

  let dataSync = createDataSync(collection, config, schema, stats, filesFolder, rejectsReporter, uploadFilesCacheFile)

  // add the items to the data sync, and if the internal queues are full, wait for space for the next item
  for (item of data)
    await dataSync.handleItem(item);
  
  // mark that there are no more items to be imported, to flush all internal buffers
  dataSync.noMoreItems();
  
  // wait for the sync process to complete
  await dataSync.done();
}
```
                                          
## Formal types

### createDataSync

```typescript
export interface Config {
  siteUrl: string, // url of the home page of the site
  secret: string // the value of the velo-sync secret as defined in the secret manager
}

type FieldType =
        'string'
        | 'number'
        | 'boolean'
        | 'Image'
        | 'Datetime'
        | 'Time'
        | 'RichText'
        | 'Reference'
        | 'URL'
        | 'Document'
        | 'Video'
        | 'Audio'
        | 'Address'
        | 'Tags'
        | 'Array'
        | 'Object'
        | 'Gallery';

export interface Schema {
  keyField: string, // the name of the key field in the input objects, to match with _id
  fields: {
    [key: string]: FieldType // the fields of the input object and how to transform them to velo types
  }
}

export interface Statistics {
  reportProgress(who: string, items: number) // used by the velo sync to report progress of different stages
  print(); // used to trigger printing the statistics. for API usage, can be implemented as no op.
}

export interface RejectsReporter {
  reject(item: any, error: Error): void; // used to report an item that is invalid for some reason
}

export interface Next<T> {
  handleItem: (item: T) => Promise<void> // used to add an item to the sync process
  noMoreItems: () => void // used to trigger the sync process that no more items are coming
}

export interface DataSync extends Next<Record<string, any>>{
  done(): Promise<void>; // used to wait for the sync process to complete
}


export declare function createDataSync(
    collection: string, // name of the collection to sync with 
    config: Config, // the config object 
    schema: Schema, // the schema object  
    stats: Statistics, // statistics implementation
    filesFolder: string, // base folder for relative file names 
    rejectsReporter: RejectsReporter, // rejects reporter
    dryrun: boolean, // trigger dry-run
    uploadFilesCacheFile: string = '.upload-cache.db'): DataSync
```