# Regex Notes

Reference: RegexOne.com

`\d`: any digit

`\D`: any non-digit character

`.`: wild card, any character

`\.`: period character

`[abc]`: single character that's only a,b,c

`[^abc]`: single character that's not a,b,c

`[0-6]`: single character that's from 0 to 6

`[^n-p]`: single character that's not from n to p

`\w`: single character, any alphanumeric

`\W`: single character, any non-alphanumeric

`{m}`: m repetitions, for instance `a{3}` will match "a" 3 times

`{m,n}`: m to n repetitions

`*`: 0 or more repetitions, for instance `a*` will match 0 or more repetitions of "a"

`+`: 1 or more repetitions

`?`: optional character, for instance `ab?c` will match either "abc" or "ab"

`\s`: any whitespace

`\S`: any non-whitespace character

`\t`: tab

`\n`: new line

`^abc`: a line start with "abc", different from `^` within `[]`

`abc$`: a line end with "abc"

`(...)`: capture group, for instance `^(file.*).pdf` will capture the filenames `file_record` and `file_1014` from (`file_record.pdf`, `file_1014.pdf`)

`(a(bc))`: capture subgroup, will return `abc` and `bc`

`(.*)`: capture all

`(abc|def)`: capture "abc" or "def"

**Back referencing**: allow you to reference the captured groups by using

- `\0`: the full matched text
- `\1`: group 1
- `\2`: group 2