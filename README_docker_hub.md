# IPFS - S3

IPFS client with builtin S3 storage plugin.

Fork of IPFS client: https://github.com/naure/go-ipfs

The S3 plugin: https://github.com/ipfs/go-ds-s3

# Get started

Generate the default config and a new peer identity in a directory `ipfs_data`:

    docker run --rm -v $PWD/ipfs_data:/data/ipfs naure/ipfs-s3 version

Grab the config file and clean up:

    cp ipfs_data/config config_init
    rm -rf ipfs_data

Add your S3 details in the file `config_init`. Change the object `"Datastore": { "Spec": {"mounts": [` to this:

```
  "Datastore": {
    "StorageMax": "10GB",
    "Spec": {
      "mounts": [                                            ---- FROM HERE… ----
        {
          "child": {
            "bucket": "    YOUR BUCKET    ",
            "rootDirectory": "    A BUCKET SUBDIRECTORY    ",
            "region": "    YOUR REGION    ",
            "accessKey": "    YOUR AWS ACCESS KEY    ",
            "secretKey": "    YOUR AWS SECRET KEY    ",
            "type": "s3ds"
          },
          "mountpoint": "/blocks",
          "prefix": "s3.datastore",
          "type": "measure"
        },
        {
          "child": {
            "compression": "none",
            "path": "datastore",
            "type": "levelds"
          },
          "mountpoint": "/",
          "prefix": "leveldb.datastore",
          "type": "measure"
        }
      ],                                                              ---- …TO HERE ----
      "type": "mount"
    },
  },
```

Then run the daemon. The first time, it will initialize a new data repository using `config_init`. The directory `/data/ipfs` should persist.

    docker run --net=host -v $PWD/ipfs_data:/data/ipfs -v $PWD/config_init:/config_init -e INIT_ARGS=/config_init naure/ipfs-s3

To test, add an IPFS command after the docker command, e.g.: `cat /ipfs/QmS4ustL54uo8FzR9455qaxZwuMiUhyvMcX9Ba8nUH4uVv/readme`
