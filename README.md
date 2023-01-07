# slidee

Web client and [PocketBase](https://pocketbase.io) schema for a creative slideshow platform.

Slideshows are made using HTML and CSS.

## Features

- User accounts
- Users can create slideshows
  - Slideshows are shown on the user dashboard (`./src/routes/[host]/+page.svelte`)
  - Only slideshow owner can delete or edit slideshows, anybody can view them

## Repository Structure

- Connection management page: `./src/routes/+page.svelte`
- Client Server Connection: `./src/routes/[host]/`
  - Server auth: `./src/routes/[host]/auth`
  - Slides: `./src/routes/[host]/slides/`
    - Slide view: `./src/routes/[host]/slides/[slidesid]/view`
    - Slide editor: `./src/routes/[host]/slides/[slidesid]/edit`
    - Create new: `./src/routes/[host]/slides/new`
- Reusable components: `./src/routes/lib/components`
  - Site footer: `./src/routes/lib/components/Footer.svelte`
