# api documentation for  [datauri (v1.0.5)](https://github.com/data-uri/datauri#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-datauri.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-datauri) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-datauri.svg)](https://travis-ci.org/npmdoc/node-npmdoc-datauri)
#### Create DataURI scheme easily

[![NPM](https://nodei.co/npm/datauri.png?downloads=true)](https://www.npmjs.com/package/datauri)

[![apidoc](https://npmdoc.github.io/node-npmdoc-datauri/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-datauri_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-datauri/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-datauri/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-datauri/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Helder Santana"
    },
    "bugs": {
        "url": "https://github.com/data-uri/datauri/issues"
    },
    "dependencies": {
        "image-size": "^0.3.5",
        "mimer": "^0.2.1",
        "semver": "^5.0.3"
    },
    "description": "Create DataURI scheme easily",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "d0975d1ab6c8f2e0ce3ca43baa4539be12d289a0",
        "tarball": "https://registry.npmjs.org/datauri/-/datauri-1.0.5.tgz"
    },
    "engines": {
        "node": ">= 0.10"
    },
    "homepage": "https://github.com/data-uri/datauri#readme",
    "keywords": [
        "datauri",
        "data uri",
        "data",
        "uri",
        "data-uri",
        "optimization",
        "uri",
        "optimize",
        "inline",
        "png",
        "jpg",
        "woff",
        "base64"
    ],
    "license": "MIT",
    "maintainers": [
        {
            "name": "helder",
            "email": "heldr@me.com"
        }
    ],
    "name": "datauri",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/data-uri/datauri.git"
    },
    "scripts": {},
    "version": "1.0.5"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module datauri](#apidoc.module.datauri)
1.  object <span class="apidocSignatureSpan">datauri.</span>api
1.  object <span class="apidocSignatureSpan">datauri.</span>css
1.  object <span class="apidocSignatureSpan">datauri.</span>uri

#### [module datauri.api](#apidoc.module.datauri.api)
1.  [function <span class="apidocSignatureSpan">datauri.api.</span>default ()](#apidoc.element.datauri.api.default)

#### [module datauri.css](#apidoc.module.datauri.css)
1.  [function <span class="apidocSignatureSpan">datauri.css.</span>default (data)](#apidoc.element.datauri.css.default)

#### [module datauri.uri](#apidoc.module.datauri.uri)
1.  [function <span class="apidocSignatureSpan">datauri.uri.</span>default {{signature}}](#apidoc.element.datauri.uri.default)



# <a name="apidoc.module.datauri"></a>[module datauri](#apidoc.module.datauri)



# <a name="apidoc.module.datauri.api"></a>[module datauri.api](#apidoc.module.datauri.api)

#### <a name="apidoc.element.datauri.api.default"></a>[function <span class="apidocSignatureSpan">datauri.api.</span>default ()](#apidoc.element.datauri.api.default)
- description and source-code
```javascript
class Api extends _stream2.default {
  constructor() {
    super();

    this.readable = true;
  }

  format(fileName, fileContent) {
    const fileBuffer = fileContent instanceof Buffer ? fileContent : new Buffer(fileContent);

    this.base64 = fileBuffer.toString('base64');
    this.createMetadata(fileName);

    return this;
  }

  createMetadata(fileName) {
    this.fileName = fileName;
    this.mimetype = (0, _mimer2.default)(fileName);
    const mimetype = this.mimetype;
    var _base = this.base64;
    const base64 = _base === undefined ? '' : _base;

    this.content = (0, _uri2.default)({ mimetype, base64 });

    return this;
  }

  runCallback(handler, err) {
    if (err) {
      return handler(err);
    }

    handler.call(this, null, this.content, this);
  }

  encode(fileName, handler) {
    return this.async(fileName, err => handler && this.runCallback(handler, err));
  }

  async(fileName, handler) {
    const base64Chunks = [];
    const propagateStream = chunk => this.emit('data', chunk);

    propagateStream(this.createMetadata(fileName).content);
    (0, _fs.createReadStream)(fileName, { encoding: 'base64' }).on('data', propagateStream).on('data', chunk => base64Chunks.push
(chunk)).on('error', err => {
      handler(err);
      this.emit('error', err);
    }).on('end', () => {
      this.base64 = base64Chunks.join('');
      this.emit('end');
      handler.call(this.createMetadata(fileName));
      this.emit('encoded', this.content, this);
    });
  }

  encodeSync(fileName) {
    if (!fileName || !fileName.trim || fileName.trim() === '') {
      throw new Error('Insert a File path as string argument');
    }

    if ((0, _fs.existsSync)(fileName)) {
      let fileContent = (0, _fs.readFileSync)(fileName);

      return this.format(fileName, fileContent).content;
    }

    throw new Error('The file ${ fileName } was not found!');
  }

  getCSS() {
    let config = arguments.length > 0 && arguments[0] !== undefined ? arguments[0] : {};

    if (!this.content) {
      throw new Error('Create a data-uri config using the method encodeSync');
    }

    config.class = config.class || _path2.default.basename(this.fileName, _path2.default.extname(this.fileName));
    config.background = this.content;

    if (config.width || config.height || config['background-size']) {
      config.dimensions = (0, _imageSize2.default)(this.fileName);
    }

    return (0, _css2.default)(config);
  }
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.datauri.css"></a>[module datauri.css](#apidoc.module.datauri.css)

#### <a name="apidoc.element.datauri.css.default"></a>[function <span class="apidocSignatureSpan">datauri.css.</span>default (data)](#apidoc.element.datauri.css.default)
- description and source-code
```javascript
data => BASE_CSS(data).concat(SIZE(data), ['}']).join('\n')
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.datauri.uri"></a>[module datauri.uri](#apidoc.module.datauri.uri)

#### <a name="apidoc.element.datauri.uri.default"></a>[function <span class="apidocSignatureSpan">datauri.uri.</span>default {{signature}}](#apidoc.element.datauri.uri.default)
- description and source-code
```javascript
data => 'data:${ data.mimetype };base64,${ data.base64 }'
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
