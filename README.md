# Generate block documentation
This module to automatically generate documentation for backend development based on [TypeScript](https://www.typescriptlang.org/) interfaces. It uses the [Typhen](https://github.com/shiwano/typhen) module to scan the interfaces and then parses it into a re-usable json object 

## Setup
Clone the repository and then run:

```
npm i
```
## Demo
To preview output run the following script

```
npm run example
```

## Methods

### generate(options:Object):void
Fully generate the block documentation include a index.html file to preview the generated output, see the [configuration](#documentation) for the available options

### generateData(options:object):void
Only output the data.json file so you can have your own custom template for displaying generated output, see the [configuration](#documentation) for the available options


## Documentation

### Configuration
When running the generate method you can pass along an options:

- [required][*object*] **Options** The root options object
	- [required][*string|object*] **input**: The root folder where the task will look for blocks/files
	- [required][*string*] **output**: The output folder where the documentation will be generated
	- [*string*] **jsonFile**: The name of the file that contains all the documentation
	- [*string*] **interfaceName**: The template used to find the interface file {blockId} will be replaced with the folder name
	- [*string*] **exampleBlockIdLabel**: This is the label that's used for generating the example output
	- [*string*] **silent**: Run the module without any logs
	- [*object*] **placeholderValues**: Object containing the values that are used when no ``@placeholder`` is provided
		- [*string*] **string**: The default placeholder value of a string
		- [*boolean*] **boolean**: The default placeholder value of a boolean
		- [*number*] **number**: The default placeholder value of a number

#### Input configuration
The input can be defined in a `string` or an `object`. If you want to skip the folder input completely you can just define some files that will be scaffolded.

**Direct path to folder**

```javascript
{
	input: './input/'
}
```

**Direct path to folder with extra files**

```javascript
{
	input: {
		folder: './input/',
		files: [
			'./foo/IFooFile.ts',
			'./bar/IBarFile.ts',
		]
	}
}
```

**Only files defined**

```javascript
{
	input: {
		files: [
			'./foo/IFooFile.ts',
			'./bar/IBarFile.ts',
		]
	}
}
```

### Creating interfaces
All blocks have an interface which describes the data required for the block to provide the data used in the documentation you can use a couple of [YUIDoc](http://yui.github.io/yuidoc/) comments

#### Used YUI doc comments
* ``@placeholder``: This will be used to overwrite the predefined placeholder value
* ``@ignore``: If this comment is available it will be skipped
* ``@description``: This is the description about the property
* ``@defaultValue``: If a default value is present you can provide it with this comment
* ``@rawName``: When you use a stringLiteral to type a property you need to define the raw name of the property (see the [example interface](#example-interface))

## Examples

### Example folder structure
    Root folder
    ├── block-foo
    │   └── IBlockFooOptions.ts
    ├── block-bar
    │   └── IBlockBarOptions.ts
    └── ...
    
#### Example Interface

```typescript
import IImage from "./interface/IImage";
import Theme from "./enum/Theme";

interface IBlockDummyOptions
{
	/**
	 * @ignore
	 * @description This is the Id of the interface that will be ignored by the generate task
	 */
	id:string;
	/**
	 * @description The heading displayed
	 * @defaultValue Define the default value of the property if required
	 * @placeholder This is a defined placeholder value
	 */
	header:string;
	/**
	 * @description The image is referenced to another interface
	 */
	image:IImage
	/**
	 * @description The theme is referenced to an external Enum
	 */
	theme?:Theme
	/**
	 * @description A forced string value
	 * @rawName definedString
	 */
	definedString: "stringValue1" | "stringValue2";
}

export default IBlockDummyOptions
```

#### Example configuration

```javascript
const blockDocumentation = require('block-documentation');

blockDocumentation.generate({
    input: {
    	folder: './input/',
    	files: [
    		'./path/to/file.ts'
    	]
    },
    output: './output/',
    jsonFile: 'data.json',
    interfaceName: 'I{blockId}Options.ts',
    placeholderValues: {
        string: 'Lorem ipsum dolor sit amet',
        boolean: true,
        number: 1
    }
})
```