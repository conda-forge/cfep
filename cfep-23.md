<table>
<tr><td> Title </td><td> Migration to the Zulip chat platform </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Jaime Rodríguez-Guerra &lt;jaime.rogue@gmail.com&gt; Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sep 20, 2024</td></tr>
<tr><td> Updated </td><td> Sep 20, 2024</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/cfep/pull/54 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

The conda-forge core team is proposing to transition chat platforms from Element/Matrix to Zulip.

## Background

Initially, conda-forge offered support through the Gitter platform, which allowed one chat room
per repository. `conda-forge/conda-forge.github.io` was chosen for this and many users have received support over the years in the corresponding room. As of September 2024, over 2000 users have joined the room. The conda-forge core team also used private rooms to communicate core issues. However, due to the lack of encryption in Gitter, a Keybase room was created to exchange information about sensitive topics.

When Gitter was integrated in the Matrix protocol and migrated to the Element client, it gained encryption features. This meant that security sensitive items could be discussed in the regular private rooms. As a result, Keybase was abandoned and its contents migrated to a secure secret management vault.

The integration in the Element ecosystem was seen as exciting and positive: emoji reactions were available for the first time, and threads were shortly implemented too. However, over time the excitement faded away, which prompted the proposal discussed in this CFEP.

## Motivation

Element/Matrix offers a free to use real-time chat platform with web, desktop and mobile clients with end-to-end encryption messaging. Each community or space can create one or more rooms, public or private, to discuss different topics. Other features like videocalls, private messaging, and file sharing are available. Users can create a new account or log in with their Github credentials, among others.

However, it suffers from a few issues that limit its usability within our community. The main concerns are:

- End-to-end encryption fails sometimes, leaving the users unable to read previous messages, or have their messaged read by others. Synchronization of keys across clients is necessary to fix it, but in some other cases the issue has dissapeared spontaneously.
- Some messages are considered unread for months at a time, and there's no way to find them and mark them as read.
- The thread system offers very limited ways of tracking conversations and making them discoverable. It's very difficult to follow on topics started hours ago, let alone days or weeks ago.
- Scoped discussions require a lot of discipline from the users, since threads are optional, and the real-time chat is preferred in the UI.

Zulip, on the other side, is a free to use, open source, real-time discussion board. It has web, desktop and mobile clients. Each community has its Zulip instance, where several "channels" can be created. Each channel hosts a number of threads or topics. Instant messaging is also available within the Zulip instance. Users can also create a new account or log in with their Github credentials, among others.

Zulip offers some features we would consider beneficial to the conda-forge community:

- Threads (or topics) are encouraged and the default user experience.
- Each topic must have a title that promotes well-scoped discussions, which can be marked as solved when the issue has been addressed.
- Moderation tools are available to split threads into new ones in case the conversation goes off-topic.
- Users can "follow" threads they find interesting, which has its own dedicated view. This makes it easier to track updates to conversations you have started or participated in.
- Private rooms are also available, but they don't offer end-to-end encryption. For this matter, a sensitive communications protocol is proposed below.

## Proposal

After accepting this proposal, we will implement the following decisions:

- The Zulip instance published at `conda-forge.zulipchat.com` will be open to the public with world-readable permissions.
- The General channel will be the replacement for the `conda-forge/conda-forge.github.io` chat room. This will be reflected in the conda-forge.org documentation, and reminded periodically in the Element room.
- The conda-forge core team will move its private communications to the Zulip instance, and stop using the Element rooms. A last message will be published with the details on how to transition to the Zulip instance.
- New channels will be created in the instance as considered necessary (e.g. infrastructure, compilers, etc).
- The Zulip instance will be treated as a public message board. No private chat or private rooms for folks besides admins or owners will be enabled.
- Sensitive topics will NOT be discussed directly in the Zulip instance. Instead they will follow the protocol described below.

Three months after the approval of this CFEP, the core team will consider the migration finished and the public rooms will become private and archived. If the core team estimates that there's still sufficient activity, another three months will be granted, with periodic reminders of the new Zulip instance. After that period, no more extensions will take place.

## Sensitive communications protocol

Since Zulip doesn't have end-to-end encryption, it is not the best place to discuss sensitive matters. Instead, we propose the following protocol.

1. Core members will announce that a new sensitive topic needs to be discussed in the Zulip channel for `core`. A small description describing the situation without revealing sensitive details should be shared.
2. A new private room will be created in Matrix and announced in the private topic. Interested core members can voluntarily join the room to participate or observe the conversation.
3. When the issue has been addressed, a summary of the events and decisions taken will be captured in the secret vault. The notes document should contain the date, topic title, participants and any other necessary information.
4. Once the summary has been safely stored, a short announcement should be posted to the Zulip topic informing about the publication of the relevant notes in the vault.
5. After a short period has passed with no comments, the Element room will be deleted and the Zulip topic will be marked as solved.

## Alternatives

The community also suggested moving to Discord. However that was discarded given its closed-source nature, handling of the messages there exchanged, and the need of a Discord account. A broader scope discussion can be found at [`conda/communications#29`](https://github.com/conda/communications/issues/29).

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
