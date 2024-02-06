---
title: Configuration
order: 2
---

## The .pages.yml file

To configure Pages CMS you just need to add a `.pages.yml` file to the repository (and branch) that hosts your content and media. If this file isn't there when you open your repository/branch in Pages CMS, you will be be presented with a link to add one via the Pages CMS interface.

You can have different configuration files on separate branches. The Pages CMS interface allows you to navigate between them (click on the name of repository at the top of the sidebar and then select the branch in the dropdown menu).

The `.pages.yml` file contains mainly 2 sections:

- **media**: the settings for media (images, videos, etc). [See the "Media" section below](#media).
- **content**: an array defining the content types. [See the "Content" section below](#content).

## Media

With media, you can configure how to handle the files that can be embedded in or associated with the content: attachments, thumbnails, inline images...

### Keys

| Key | Type | Description |
| - | - | - |
| **`input`**| `string` | The path to the media folder relative to the root of the repo (e.g. `src/files/media`). This path is what allows us to find the files in Pages CMS to manage content and media. |
| **`output`** | `string` | The path to the media folder relative to the root of the website (e.g. `files/media`). This path will prefix all media saved in our content, which will be used by your static site generator. |
| **`path`** | `string` | The default path to present the user (e.g. when opening the media browser on an [Image Field](/docs/configuration/image-field)). |
| **`extensions`** | `string` | An array of file extensions that should be displayed. If provided, any file with an extension not included in this list will not be shown to the user. |
| **`categories`** | `string` | Similar to `media.extensions`, but using categories of files: `image` (`jpg`, `jpeg`, `png`, `gif`, `svg`, `bmp`, `tif`, `tiff`), `document` (`pdf`, `doc`, `docx`, `ppt`, `pptx`, `vxls`, `xlsx`, `txt`, `rtf`), `video` (`mp4`, `avi`, `mov`, `wmv`, `flv`), `audio` (`mp3`, `wav`, `aac`, `ogg`, `flac`) and `compressed` (`zip`, `rar`, `7z`, `tar`, `gz`, `tgz`). If both `media.extensions` and `media.categories` are provided, `media.categories` will be ignored. |

If `media` is set to a `string`, it is equivalent to settings both `media.input` and `media.output` to that value, prefixed with `/` for `media.output`.

For example, `media: files/media` would be equivalent to:

```yaml
media:
  input: files/media
  output: /files/media
```

### Examples

Let's assume the content for my website is at the root of my repo:

```yaml
media: files/media
```

Now, if the content for this website was in a `src/` subfolder, it could look like this:

```yaml
media:
  input: src/media
  output: media
```

Let's assume now that I'm hosting my website in a subfolder (e.g. hosted at `http://example.com/my-website/`), I may want to do the following:

```yaml
media:
  input: src/media
  output: /my-website/media
```

## Content

Content managed by the users: collections (e.g. blog posts) and single types (e.g. the home page). The `content` key should be set as an array of content entries.

### Keys

Each content entry can define the following keys:

| Key | Type | Description |
| - | - | - |
| **`name`** | `string` | **Required and must be unique across the content array**. Machine name for the content entry. |
| **`label`** | `string` | Display name for the collection or single file. This will be displayed in the main menu. |
| **`type`**| `string` | **Required**. `collection` or `single`, depending on whether the content entry is a collection of files with an identical schema (e.g. blog posts) or a single file (e.g. home page). |
| **`path`** | `string` | **Required**. Path to the folder where the files are stored if it's a collection (e.g. `path: src/posts`, otherwise the path to the single file (e.g. `path: src/index.md`). |
| **`fields`** | `string` | The list of fields defining the schema of the content entry (e.g. title, date, author, body, etc). [See the "Fields" section below](#fields). |
| **`filename`** | `string` | The pattern to generate the filename when creating a new file. You can use the value of any field (e.g. `fields.title`) including nested values (e.g. `fields.tags[0].label`). You can also use a few date tokens (`{year}`, `{month}`, `{day}`) and time (`{hour}`, `{minute}`, `{second}`) and `{primary}` for the primary field as defined in the `view` key. By default this is set to `'{year}-{month}-{day}-{primary}.md'`. |
| **`view`** | `object` | **Only valid for collections**. This object defines the various options for the collection view; visible fields, sorting options and defaults, fields indexed for the search... [See the "View" section below](#view). |
| **`editor`** | `string` | The type of editor that should be used to edit the content: `yfm`, `yaml`, `json`, `datagrid`, `code` or `raw`. It defaults to yaml-front-matter |
| **`subfolders`** | `boolean` | Whether or not the collection should display subfolders. Default to `true`. Set to `false` if you want to force the collection of files to be "flat". |

### Examples

Let's assume we have a simple collection of blog posts all saved in the `src/_posts` folder:

```yaml
content:
  name: posts
  label: Posts
  path: src/_posts
  fields:
    (...)
```

## View

The `view` object is only read for `type: collection` content entries. It defines the configuration for the collection view, aka the page that lists the entries for a collection which is accessed from the main menu under a specific repo/branch (e.g. `https://app.pagescms.org/pages-cms/template-nextjs/content/posts/`).

![Collection view](/media/screenshots/collection-dark.png)

### Keys

| Key | Type | Description |
| - | - | - |
| **`fields`** | `array` | List of the fields to be displayed in the collection view (e.g. `fields: [ title, published, author ]`). This can include nested fields and values (e.g. `fields: [ title, published, "tags[0]", author.name ]`). If not defined, it defaults to just the primary field (see below). The order of the fields is kept in the collection view. |
| **`primary`** | `string` | The name of the field that should be used as the primary field (e.g. `primary: title`). If undefined, it will be set to the `title` field if it exists, otherwise the first field define in the content entry (e.g. `content.posts.fields[0].name`). |
| **`sort`** | `array` | The list of fields that the collection can be sorted by (e.g. `sort: [ date, title ]`). By default, it is set to the date (if any) and the primary field. |
| **`search`** | `array` | The list of fields that should be indexed for search. By default, all fields are indexed. |
| **`default`** | `object` | Define the default values for search and sorting: `{ search: 'My keywords', sort: title, order: asc }`). `default.order` can be `asc` or `desc`. By default, `default.search` is empty, `default.sort` is the first field in the `sort` array and `default.order` is set to `desc`. |

### Examples

Assuming you have a `date` field and `title` is the primary field, your default configuration would look like:

```yaml
view:
  fields: [ title ]
  primary: title
  sort: [ date, title ]
  default:
    search: ''
    sort: title
    order: desc
```

This would display the title of each post and sort them by title in descendant order (start with special characters and numbers, all the way to `z`).

Let's assume we also have the following:

- `published`, a boolean that defines whether or not the post is published,
- `author`, a string set to the name of the author.

Let's set the view to only display the posts from `Patricia`, display title, date and the published state with the newer posts first:

```yaml
view:
  fields: [ title, date, published ] # title is the first entry and thus will be set to primary
  sort: [ date, title, published ]
  default:
    search: 'author:Patricia'
    sort: date
    sort: desc
```

The `author:Patricia` syntax [comes from lunr.js](https://lunrjs.com/guides/searching.html#fields), the search library used by Pages CMS under the hood. Other syntax will work too (wildcards, boosts, fuzzy matches and term presence).


## Fields

### Keys

| Key | Type | Description |
| - | - | - |
| **`name`** | `string` | **Required and must be unique across the fields array**. Machine name for the field. **`body` is reserved for the body of the file when deal with a frontmatter file (e.g. YAML frontmatter)**. |
| **`label`** | `string` | Display name for the field. This is what is displayed in the edit form. |
| **`description`** | `string` | Default value. |
| **`type`** | `string` | Defines the type of field: **[boolean](#boolean-field)**, **[code](#code-field)**, **[date](#date-field)**, **[image](#image-field)**, **[number](#number-field)**, **[object](#object-field)**, **[rich-text](#rich-text-field)**, **[select](#select-field)**, **[string](#string-field)** or **[text](#text-field)**. If undefined or set to a field that doesn't exist, it defaults to `text`. |
| **`default`** | | Default value. |
| **`list`** | `boolean` or `object` | If truthy, the field is an array of values (of the type defined for the field). [See the "View" section below](#view). |
| **`hidden`** | `boolean` | If `true`, the field will not be displayed in the form but will be saved. It is usually used with `default` to set a required field that shouldn't be edited by users, like for example the language of a post (`lang: en-US`). |
| **`fields`** | `array` | **Only valid for object fields**. List of the fields in that object. |
| **`options`** | `object` | Options for that field. Refer to the field specific details below. |

### List

Any field with a truthy `list` key will be stored as an array of that field type. For example:

```yaml
name: images
label: Images
type: image
list: true
```

Will allow the user to save multiple image paths:

```yaml
images:
  - media/image-1.png
  - media/image-2.png
```

`list` can be an object with `min`, `max` and `default` keys. `min` and `max` define the minimum and maximum number of entries in the array. For example:

```yaml
name: images
label: Images
type: image
list:
  min: 1
  max: 4
```

This will force the user to enter at least 1 image and at most 4.

<!-- Certain fields (e.g. `type: image`) implement their own list logic. If you want to use the default list widget, you may set `default` to true. -->