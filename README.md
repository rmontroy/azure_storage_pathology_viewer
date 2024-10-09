# Azure Storage pathology viewer
This is a reference implementation for viewing Aperio SVS digital slide images that are stored in Azure Storage Blobs.

## Front-end web client
The front-end [(web)](/web) uses [OpenSeadragon](https://openseadragon.github.io/) and is meant to be embedded in an existing application. Once the back-end is deployed, set the service endpoint in `config.js`, and then you can test the front-end in standalone mode like so:

`npx http-server web`  
or  
`npx serve web`  
or  
`npx servor --static web`

## Back-end API/service
The back-end [(api)](/api) is deployed via Azure Bicep. It creates an Azure Functions app that translates [IIIF](https://iiif.io/api/image/3.0/) image requests to byte-range fetches against the .svs file in an Azure Storage blob. It relies on a [modified implementation of OpenSlide](https://github.com/rmontroy/openslide/tree/azure) to read the .svs files from an Azure Storage blob.  
This implementation uses Azure API Management with an API mapping to a custom domain.

## Demo
(_Courtesy https://openslide.org/formats/aperio/#test-data_)

https://vanandelinstitute.github.io/s3vs/?imageId=CMU-1  
https://vanandelinstitute.github.io/s3vs/?imageId=CMU-2  
https://vanandelinstitute.github.io/s3vs/?imageId=CMU-3  
https://vanandelinstitute.github.io/s3vs/?imageId=JP2K-33003-1  
https://vanandelinstitute.github.io/s3vs/?imageId=JP2K-33003-2  
