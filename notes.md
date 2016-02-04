- Use variable reference

$user = User::create([

]);


User::find($user->getKey())

- findBy with operators

- clases -> classes typo



- mention operators

- terminology: find -> returns one, get/paginate returns many

- do not reference `id`, use `name` or `email` instead

- "It is important to note that fill()" as highlighted note instead of within the paragraph

- update and delete sub-sections

- User::find($id)->delete()

- explain soft deletes

- add `use` statement to `SoftDelete`

- switch Take and Skip with Limit and Offset

- use multi-line code snippets

- count example with a query (such as where)

- count is an executing function
