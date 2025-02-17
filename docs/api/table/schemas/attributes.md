The following attribute properties are supported:

| Property | Type | Description |
| -------- | :--: | ----------- |
| crypt | `boolean` | Set to true to encrypt the data before writing. |
| default | `string` | Default value to use when creating model items or when reading items without a value.|
| enum | `array` | List of valid string values for the attribute. |
| filter | `boolean` | Enable a field to be used in a filter expression. Default true. |
| generate | `string|boolean` | Set to 'ulid' or 'uuid' to automatically create a new ID value for the attribute when creating new items. Set to true to use a custom ID generator defined via the Table params.generate option. Default to null. |
| hidden | `boolean` | Set to true to omit the attribute in the returned Javascript results. Attributes with a "value" template defined will by hidden by default. Default to the schema params value. |
| isoDates | `boolean` | Set to true to store dates as Javascript ISO strings vs epoch numerics. If unset, the field will use the table default value for isoDates. Default to the schema params value. |
| map | `string` | Map the field value to a different attribute name when storing in the database. Can be a simple attribute name or a compound "obj.name" where multiple fields can be stored in a single attribute containing an object with all the fields. |
| nulls | `boolean` | Set to true to store null values or false to remove attributes set to null. Default false. |
| required | `boolean` | Set to true if the attribute is required. Default to the schema params value. |
| reference | `string` | Describes a reference to another entity item. Format is: model:index:attribute=src-attribute,... |
| schema | `object` | Nested schema. |
| ttl | `boolean` | Set to true to store the date value as a Unix epoch in seconds suitable for use as a DynamoDB TTL attribute. |
| type | `Type or string` | Type to use for the attribute. |
| unique | `boolean` | Set to true to enforce uniqueness for this attribute. Default false. |
| validate | `RegExp` | Regular expression to use to validate data before writing. |
| value | `string` | Template to derive the value of the attribute. These attributes are "hidden" by default. |


If the `default` property defines the default value for an attribute. If no value is provided for the attribute when creating a new item, the `default` value will be used.

If the `hidden` property is set to true, the attribute will be defined in the DynamoDB database table, but will be omitted in the returned Javascript results.

If the `isoDates` property is defined and not-null, it will override the table isoDates value. Set to true to store the field date value as an ISO date string. Set to false to store the date as a Unix epoch date number.

The `map` property can be used to set an alternate or shorter attribute name when storing in the database. The map value may be a simple string that will be used as the actual attribute name.

Alternatively, the map value can be a pair of the form: 'obj.name', where the attribute value will be stored in an object attribute named "obj" with the given name `name`. Such two-level mappings may be used to map multiple properties to a single table attribute. This is helpful for the design pattern where GSIs project keys plus a single 'data' field and have multiple models map relevant attributes into the projected 'data' attribute. OneTable will automatically pack and unpack attribute values into the mapped attribute. Note: APIs that write to a mapped attribute must provide all the properties that map to that attribute on the API call. Otherwise an incomplete attribute would be written to the table.

The `reference` attribute documents a reference to another entity by using this attribute in combination with other attributes. The format is:

```bash
model:index:attribute=source-attribute,...
```

The "model" selects that target entity model of the reference using the nominated "index" where the target "attribute" is determined by the associated source-attribute. Multiple attributes can be specified. Tools can use this reference to navigate from one entity item to another.

The `schema` property permits nested field definitions. The parent property must be an Object as Arrays are not yet supported. Note: TypeScript typings are not created for nested schemas.

The `ttl` property supports DynamoDB TTL expiry attributes. Set to true to store a supplied date value as a Unix epoch in seconds suitable for use as a DynamoDB TTL attribute.

The `type` properties defines the attribute data type. Valid types include: String, Number, Boolean, Date, Object, Null, Array, Buffer (or Binary) and Set. The object type is mapped to a `map`, the array type is mapped to a `list`. Dates are stored as Unix numeric epoch date stamps unless the `isoDates` parameter is true, in which case the dates are store as ISO date strings. Binary data is supplied via `buffer` types and is stored as base64 strings in DynamoDB.

The `validate` property defines a regular expression that is used to validate data before writing to the database. Highly recommended.

The `value` property defines a literal string template that is used to compute the attribute value. This is useful for computing key values from other properties, creating compound (composite) sort keys or for packing fields into a single DynamoDB attribute when using GSIs.

String templates are similar to JavaScript string templates. The template string may contain `${name}` references to other fields defined in the entity model. If any of the variable references are undefined when an API is called, the computed field value will be undefined and the attribute will be omitted from the operation. The variable `name` may be of the form: `${name:size:pad}` where the name will be padded to the specified size using the given `pad` character (which default to '0'). This is useful for zero padding numbers so that they sort numerically.

If you call `find` or any query API and do not provide all the properties needed to resolve the complete value template. i.e. some of the ${var} references are unresolved, OneTable will take the resolved leading portion and create a `begins with` key condition for that portion of the value template.
