# ClickUp Sync Map

This folder is the local source for the series while ClickUp MCP access is rate-limited.

## Existing destination

- Space: `Content-Writing`
- Folder: `Dice`
- Existing list: `List`
- Existing OS folder: `Personal Brand & Content OS`
- OS lists: `Content Ideas`, `Production Pipeline`, `Content Calendar`, `Performance & Analytics`

## Safe sync rules

1. Create exactly one canonical ClickUp task per episode in the existing `Dice/List` list.
2. Use Tasks in Multiple Lists to surface those tasks in `Content Ideas`, `Content Calendar`, `Production Pipeline`, and `Performance & Analytics` when the ClickApp is enabled.
3. If Tasks in Multiple Lists is unavailable, keep the canonical episode in `Dice/List` and create only workflow tasks that link back to it; never create unrelated episode duplicates.
4. Leave start and due dates empty.
5. Reuse the existing `to do`, `in progress`, and `complete` statuses; represent detailed workflow stages in task descriptions or existing fields.
6. Add only custom fields that already exist or are proven necessary after a fresh audit.
7. Map sequence using dependencies: Episode 02 waits on 01, Episode 03 waits on 02, and so on. Add foundation links from Episodes 05–10 to Episodes 02–04 when supported.
8. Never populate scientific sources, religious sources, published URLs, or analytics with placeholders that look like real data.
