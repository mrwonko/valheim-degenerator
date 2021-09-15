# Notes

See [Valheim Save Tools](https://github.com/Kakoen/valheim-save-tools) for file format!

DB File contains generated world. Little Endian.

```
Long = int64
Double = float64

struct file {
    int32 world_version (27)
    double net_time (identical in my experiments?)
    int64 my_id
    int32 num_zdos
    zdo[num_zdos] zdos
    int32 num_dead_zdos
    zdo[num_zdos] dead_zdos
    int32 num_zones
    vector2_i[num_zones] zones
    int32 pgw_version
    int32 location_version
    int32 num_global_keys
    string[num_global_keys] global_keys
    bool locations_generated
    int32 num_prefab_locations
    prefab_location[num_prefab_locations] prefab_locations
};

struct zdo {
    zdo_id id
    int32 content_length
    char[content_length] content
};

struct zdo_content {
    uint32 owner_revision
    uint32 data_revision
    bool persistent
    int64 owner
    int64 time_created
    int32 pgw_version
    char type
    bool distant
    int32 prefab // hash of prefab name https://github.com/Kakoen/valheim-save-tools/blob/79d9f2c8fac0485aee63152e74acca300b6afead/valheim-save-tools-lib/src/main/java/net/kakoen/valheim/save/decode/StableHashCode.java#L10
    vector2i sector
    vector3f position
    quaternion rotation
    char num_floats
    entry<float32>[num_floats] floats
    char num_vector3fs
    entry<vector3f>[num_vector3fs] vector3fs
    char num_ints
    entry<int32>[num_ints] ints
    char num_longs
    entry<int64>[num_longs] longs
    char num_strings
    entry<string>[num_strings] strings
    char num_byte_arrays
    entry<varbytes>[num_byte_arrays] byte_arrays
};

struct zdo_id {
    int64 user_id
    uint32 id
};

struct prefab_location {
    string name
    vector3f position
    bool generated
};

struct vector2i {
    int32 x, y
};

struct vector3f {
    float32 x, y, z
};

quaternion = float32[4]

struct string {
    varint length
    char[length] data
};

struct varbytes {
    int32 length
    char[length] data
}

template<typename T>
struct entry {
    int32 key_hash
    T value
}

bool = char
```

varint: 7 low bits per char, top bit 1 -> more (max 4 byte). LSB first. [reference](https://github.com/Kakoen/valheim-save-tools/blob/79d9f2c8fac0485aee63152e74acca300b6afead/valheim-save-tools-lib/src/main/java/net/kakoen/valheim/save/parser/ZPackage.java#L356)

You know what? I'll not bother parsing that myself, I'll just use the JSON.
