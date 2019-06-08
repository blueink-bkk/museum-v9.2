# `tech-notes`

##### Postgres function returning an object
```
select cms_revision__commit('{
    "parent_id":278109,
    "title":"1195 Annales S Jacobi Leodiensis20131126.pdf",
    "name":"015223569a3n3l2e3s4jco2bi3d2pf",
    "data":{"dirname":"/media/dkz/"},
    "verbose":0
    }')->>'revision_id'
```

##### HTML links target attribute
- Use a different value for the target attribute for each link if you want them to open in different tabs, 
- the same value for the target attribute if you want them to replace the other ones.
- the target value doesn't have to be '_blank`
- [Frank on stackoverflow](https://stackoverflow.com/users/229535/frank)
```
<a href="http://www.starfall.com/" target="Starfall">Starfall</a>
```

##### Content-item not registerd for this folder
`ERROR:  -20000: This items content type cms-pdf is not registered to this folder 278109`
- `content_item__new`
- two steps: content_type (1) defined, (2) registered for this folder.
```
query = 'select content_type__create_type($1,$2,$3,$4,$5,$6,$7)';
plv8.execute(query,[
    it.content_type,
    it.super_type,
    it.pretty_name,
    it.pretty_plural,
    it.table_name,
    it.id_column,
    it.name_method
]);
```
or jsonb equivalent
```
query = 'select content_type__create_type($1,$2,$3,$4,$5,$6,$7)';
plv8.execute('select content_type__create_type($1),[{
    content_type,
    super_type,
    pretty_name,
    pretty_plural,
    table_name,
    id_column,
    name_method
}]);
```

##### Content_type without dashes.
- `content_type` used for `content_type__create_type` may not contains "-"
- because a table or view will be created dynamically with same name, here `pdf-page_t`
which is an invalid name.
```
"internalquery":"create table pdf-page_t (
    pdf-page_id integer 
    constraint pdf-page_t_pk primary key  
    constraint pdf-page_t_fk references cr_revisions on delete cascade
    )"
```
