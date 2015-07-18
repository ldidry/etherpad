# NAME

Etherpad - interact with Etherpad API

# VERSION

version 1.2.12.0

# SYNOPSIS

    use Etherpad;
    my $ec = Etherpad->new(
      url      => 'http://pad.example.org',
      apikey   => 'secret_etherpad_API_key',
      user     => 'http_user',
      password => 'http_password',
      proxy    => {
          http  => 'http://proxy.example.org',
          https => 'http://proxy.example.org'
      }
    );

    $ec->create_pad('new_pad_name');

# DESCRIPTION

Client module for the Etherpad HTTP API.

The Etherpad API currently supported is the 1.2.12 (Etherpad version: 1.5.6)

This module aims to replace [Etherpad::API](https://metacpan.org/pod/Etherpad::API)

# ATTRIBUTES

[Etherpad](https://metacpan.org/pod/Etherpad) implements the following attributes.

## url

    my $url = $ec->url;
    $ec     = $ec->url('http://pad.example.org');

MANDATORY. The Etherpad URL, no default.

## apikey

    my $apikey = $ec->apikey;
    $ec        = $ec->apikey('secret_etherpad_API_key');

MANDATORY. Secret API key, located in the APIKEY.txt file of your Etherpad installation directory, no default.

## ua

    my $ua = $ec->ua;
    $ec    = $ec->ua(Mojo::UserAgent->new);

OPTIONAL. User agent, default to a Mojo::UserAgent. Please, don't use anything other than a Mojo::Useragent.

## user

    my $user = $ec->user;
    $ec      = $ec->user('bender');

OPTIONAL. HTTP user, use it if your Etherpad is protected by a HTTP authentication, no default.

## password

    my $password = $ec->password;
    $ec          = $ec->password('beer');

OPTIONAL. HTTP password, use it if your Etherpad is protected by a HTTP authentication, no default.

## proxy

    my $proxy = $ec->proxy;
    $ec       = $ec->proxy({
      http  => 'http://proxy.example.org',
      https => 'http://proxy.example.org'
    });

OPTIONAL. Proxy settings. If set to { detect => 1 }, Etherpad will check environment variables HTTP\_PROXY, http\_proxy, HTTPS\_PROXY, https\_proxy, NO\_PROXY and no\_proxy for proxy information. No default.

# METHODS

Etherpad inherits all methods from Mojo::Base and implements the following new ones.

## Groups

Pads can belong to a group. The padID of grouppads is starting with a groupID like g.asdfasdfasdfasdf$test

See [http://etherpad.org/doc/v1.5.6/#index\_groups](http://etherpad.org/doc/v1.5.6/#index_groups)

### create\_group

    Usage     : $ec->create_group();
    Purpose   : Creates a new group
    Returns   : The new group ID
    Argument  : None
    See       : http://etherpad.org/doc/v1.5.6/#index_creategroup

### create\_group\_if\_not\_exists\_for

    Usage     : $ec->create_group_if_not_exists_for('groupMapper');
    Purpose   : This functions helps you to map your application group ids to epl group ids
    Returns   : The epl group id
    Argument  : Your application group id
    See       : http://etherpad.org/doc/v1.5.6/#index_creategroupifnotexistsfor_groupmapper

### delete\_group

    Usage     : $ec->delete_group('groupId');
    Purpose   : Deletes a group
    Returns   : 1 if it succeeds
    Argument  : The id of the group you want to delete
    See       : http://etherpad.org/doc/v1.5.6/#index_deletegroup_groupid

### list\_pads

    Usage     : $ec->list_pads('groupId');
    Purpose   : Returns all pads of this group
    Returns   : An array or an array reference (depending on the context) which contains the pad ids
    Argument  : The id of the group from which you want the pads
    See       : http://etherpad.org/doc/v1.5.6/#index_listpads_groupid

### create\_group\_pad

    Usage     : $ec->create_group_pad('groupID', 'padName' [, 'text'])
    Purpose   : Creates a new pad in this group
    Returns   : 1 if it succeeds
    Argument  : The group id, the pad name, optionally takes the pad's initial text
    See       : http://etherpad.org/doc/v1.5.6/#index_creategrouppad_groupid_padname_text

### list\_all\_groups

    Usage     : $ec->list_all_groups()
    Purpose   : Lists all existing groups
    Returns   : An array or an array reference (depending on the context) which contains the groups ids
    Argument  : None
    See       : http://etherpad.org/doc/v1.5.6/#index_listallgroups

## Author

These authors are bound to the attributes the users choose (color and name).

See [http://etherpad.org/doc/v1.5.6/#index\_author](http://etherpad.org/doc/v1.5.6/#index_author)

### create\_author

    Usage     : $ec->create_author(['name'])
    Purpose   : Creates a new author
    Returns   : The new author ID
    Argument  : Optionally takes a string as argument : the new author's name
    See       : http://etherpad.org/doc/v1.5.6/#index_createauthor_name

### create\_author\_if\_not\_exists\_for

    Usage     : $ec->create_author_if_not_exists_for(authorMapper [, name])
    Purpose   : This functions helps you to map your application author ids to epl author ids
    Returns   : The epl author ID
    Argument  : Your application author ID (mandatory) and optionally the epl author name
    See       : http://etherpad.org/doc/v1.5.6/#index_createauthorifnotexistsfor_authormapper_name

### list\_pads\_of\_author

    Usage     : $ec->list_pads_of_author('authorID')
    Purpose   : Returns an array of all pads this author contributed to
    Returns   : An array or an array reference depending on the context, containing the pads names
    Argument  : An epl author ID
    See       : http://etherpad.org/doc/v1.5.6/#index_listpadsofauthor_authorid

### get\_author\_name

    Usage     : $ec->get_author_name('authorID')
    Purpose   : Returns the Author Name of the author
    Returns   : The author name
    Argument  : The epl author ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getauthorname_authorid

## Session

Sessions can be created between a group and an author. This allows an author to access more than one group. The sessionID will be set as a cookie to the client and is valid until a certain date. The session cookie can also contain multiple comma-seperated sessionIDs, allowing a user to edit pads in different groups at the same time. Only users with a valid session for this group, can access group pads. You can create a session after you authenticated the user at your web application, to give them access to the pads. You should save the sessionID of this session and delete it after the user logged out.

See [http://etherpad.org/doc/v1.5.6/#index\_session](http://etherpad.org/doc/v1.5.6/#index_session)

### create\_session

    Usage     : $ec->create_session('groupID', 'authorID', 'validUntil')
    Purpose   : Creates a new session. validUntil is an unix timestamp in seconds
    Returns   : The epl session ID
    Argument  : An epl group ID, an epl author ID and an valid unix timestamp (the session validity end date)
    See       : http://etherpad.org/doc/v1.5.6/#index_createsession_groupid_authorid_validuntil

### delete\_session

    Usage     : $ec->delete_session('sessionID')
    Purpose   : Deletes a session
    Returns   : 1 if it succeeds
    Argument  : An epl session ID
    See       : http://etherpad.org/doc/v1.5.6/#index_deletesession_sessionid

### get\_session\_info

    Usage     : $ec->get_session_info('sessionID')
    Purpose   : Returns informations about a session
    Returns   : A hash reference, containing 3 keys : authorID, groupID and validUntil
    Argument  : An epl session ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getsessioninfo_sessionid

### list\_sessions\_of\_group

    Usage     : $ec->list_sessions_of_group('groupID')
    Purpose   : Returns all sessions of a group
    Returns   : Returns a hash reference, which keys are sessions ID and values are sessions infos (see get_session_info)
    Argument  : An epl group ID
    See       : http://etherpad.org/doc/v1.5.6/#index_listsessionsofgroup_groupid

### list\_sessions\_of\_author

    Usage     : $ec->list_sessions_of_author('authorID')
    Purpose   : Returns all sessions of an author
    Returns   : Returns a hash reference, which keys are sessions ID and values are sessions infos (see get_session_info)
    Argument  : An epl group ID
    See       : http://etherpad.org/doc/v1.5.6/#index_listsessionsofauthor_authorid

## Pad Content

Pad content can be updated and retrieved through the API.

See [http://etherpad.org/doc/v1.5.6/#index\_pad\_content](http://etherpad.org/doc/v1.5.6/#index_pad_content)

### get\_text

    Usage     : $ec->get_text('padID', ['rev'])
    Purpose   : Returns the text of a pad
    Returns   : A string, containing the text of the pad
    Argument  : Takes a pad ID (mandatory) and optionally a revision number
    See       : http://etherpad.org/doc/v1.5.6/#index_gettext_padid_rev

### set\_text

    Usage     : $ec->set_text('padID', 'text')
    Purpose   : Sets the text of a pad
    Returns   : 1 if it succeeds
    Argument  : Takes a pad ID and the text you want to set (both mandatory)
    See       : http://etherpad.org/doc/v1.5.6/#index_settext_padid_text

### get\_html

    Usage     : $ec->get_html('padID', ['rev'])
    Purpose   : Returns the text of a pad formatted as html
    Returns   : A string, containing the text of the pad formatted as html
    Argument  : Takes a pad ID (mandatory) and optionally a revision number
    See       : http://etherpad.org/doc/v1.5.6/#index_gethtml_padid_rev

### set\_html

    Usage     : $ec->set_html('padID', 'html')
    Purpose   : Sets the text of a pad based on HTML, HTML must be well formed.
    Returns   : 1 if it succeeds
    Argument  : Takes a pad ID and the HTML code you want to set (both mandatory)
    See       : http://etherpad.org/doc/v1.5.6/#index_sethtml_padid_html

### get\_attribute\_pool

    Usage     : $ec->get_attribute_pool('padID')
    Purpose   : Returns the attribute pool of a pad
    Returns   : A hash reference, containing 3 keys
                * numToAttrib, containing a hash reference, which keys are integers and contents are array references
                * attribToNum, containing a hash reference, which keys are string and contents are integers
                * nextNum, which content is an integer
    Argument  : Takes a pad ID (mandatory)
    See       : http://etherpad.org/doc/v1.5.6/#index_getattributepool_padid

### get\_revision\_changeset

    Usage     : $ec->get_revision_changeset('padID' [, rev])
    Purpose   : Get the changeset at a given revision, or last revision if 'rev' is not defined
    Returns   : A string, representing an etherpad changeset
    Argument  : Takes a pad ID (mandatory) and optionally a revision number
    See       : http://etherpad.org/doc/v1.5.6/#index_getrevisionchangeset_padid_rev

### create\_diff\_html

    Usage     : $ec->create_diff_html
    Purpose   : Returns an object of diffs from 2 points in a pad
    Returns   : A hash reference which keys are
                * html, which content is a string representing the diff between the two revisions
                * authors, which content is an array reference of authors
    Argument  : Takes a pad ID, a revision number to start and a revision number to end. All arguments are mandatory
    See       : http://etherpad.org/doc/v1.5.6/#index_creatediffhtml_padid_startrev_endrev

## Chat

See [http://etherpad.org/doc/v1.5.6/#index\_chat](http://etherpad.org/doc/v1.5.6/#index_chat)

### get\_chat\_history

    Usage     : $ec->get_chat_history('padID' [, start, end])
    Purpose   : Returns
                 - a part of the chat history, when start and end are given
                 - the whole chat history, when no extra parameters are given
    Returns   : An array or an array reference, depending of the context, containing hash references with 4 keys :
                 - text     => text of the message
                 - userId   => epl user id
                 - time     => unix timestamp of the message
                 - userName => epl user name
    Argument  : Takes a pad ID (mandatory) and optionally the start and the end numbers of the messages you want.
                The start number can't be higher than or equal to the current chatHead. The first chat message is number 0.
                If you specify a start but not an end, all messages will be returned.
    See       : http://etherpad.org/doc/v1.5.6/#index_getchathistory_padid_start_end

### get\_chat\_head

    Usage     : $ec->get_chat_head('padID')
    Purpose   : Returns the chatHead (last number of the last chat-message) of the pad
    Returns   : The last chat-message number. -1 if there is no chat message
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getchathead_padid

### append\_chat\_message

    Usage     : $ec->append_chat_message('padID', 'text', 'authorID', 'timestamp')
    Purpose   : Add a message to chat
    Returns   : 1 if it succeeds
    Argument  : Takes a pad ID (mandatory), a text (mandatory), an author ID (mandatory) and a timestamp (mantdatory)
    See       : Undocumented yet

## Pad

Group pads are normal pads, but with the name schema GROUPID$PADNAME. A security manager controls access of them and its forbidden for normal pads to include a $ in the name.

See [http://etherpad.org/doc/v1.5.6/#index\_pad](http://etherpad.org/doc/v1.5.6/#index_pad)

### create\_pad

    Usage     : $ec->create_pad('padID' [, 'text'])
    Purpose   : Creates a new (non-group) pad. Note that if you need to create a group Pad, you should call create_group_pad.
    Returns   : 1 if it succeeds
    Argument  : Takes a pad ID (mandatory) and optionally a text to fill the pad
    See       : http://etherpad.org/doc/v1.5.6/#index_createpad_padid_text

### get\_revisions\_count

    Usage     : $ec->get_revisions_count('padID')
    Purpose   : Returns the number of revisions of this pad
    Returns   : The number of revisions
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getrevisionscount_padid

### get\_saved\_revisions\_count

    Usage     : $ec->get_saved_revisions_count('padID')
    Purpose   : Returns the number of saved revisions of this pad
    Returns   : The number of saved revisions
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getsavedrevisionscount_padid

### list\_saved\_revisions

    Usage     : $ec->list_saved_revisions('padID')
    Purpose   : Returns the list of saved revisions of this pad
    Returns   : An array or an array reference, depending of the context, containing the saved revisions numbers
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_listsavedrevisions_padid

### save\_revision

    Usage     : $ec->save_revision('padID', ['rev'])
    Purpose   : Saves a revision
    Returns   : 1 if it succeeds
    Argument  : Takes a pad ID (mandatory) and optionally a revision number
    See       : http://etherpad.org/doc/v1.5.6/#index_saverevision_padid_rev

### get\_users\_count

    Alias for pad_users_count (see below)

### pad\_users\_count

    Usage     : $ec->pad_users_count('padID')
    Purpose   : Returns the number of user that are currently editing this pad
    Returns   : The number of users
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_paduserscount_padid

### pad\_users

    Usage     : $ec->pad_users('padID')
    Purpose   : Returns the list of users that are currently editing this pad
    Returns   : An array or an array reference, depending of the context, containing hash references with 3 keys : colorId, name and timestamp
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_padusers_padid

### delete\_pad

    Usage     : $ec->delete_pad('padID')
    Purpose   : Deletes a pad
    Returns   : 1 if it succeeds
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_deletepad_padid

### copy\_pad

    Usage     : $ec->copy_pad('sourceID', 'destinationID' [, 1])
    Purpose   : Copies a pad with full history and chat. If force is true and the destination pad exists, it will be overwritten
    Returns   : 1 if it succeeds
    Argument  : A source pad ID
                A destination pad ID
                A force flag : a value which is true or false, in perl interpretation (for example; 0 and '' are false, 1, 2 and 'foo' are true)
    See       : http://etherpad.org/doc/v1.5.6/#index_copypad_sourceid_destinationid_force_false

### move\_pad

    Usage     : $ec->move_pad('sourceID', 'destinationID' [, 1])
    Purpose   : Moves a pad. If force is true and the destination pad exists, it will be overwritten
    Returns   : 1 if it succeeds
    Argument  : A source pad ID
                A destination pad ID
                A force flag : a value which is true or false, in perl interpretation (for example; 0 and '' are false, 1, 2 and 'foo' are true)
    See       : http://etherpad.org/doc/v1.5.6/#index_movepad_sourceid_destinationid_force_false

### get\_read\_only\_id

    Usage     : $ec->get_read_only_id('padID')
    Purpose   : Returns the read only link of a pad
    Returns   : A string
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getreadonlyid_padid

### get\_pad\_id

    Usage     : $ec->get_pad_id('readOnlyID')
    Purpose   : Returns the id of a pad which is assigned to the readOnlyID
    Returns   : A string
    Argument  : A read only ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getpadid_readonlyid

### set\_public\_status

    Usage     : $ec->set_public_status('padID', 'publicStatus')
    Purpose   : Sets a boolean for the public status of a pad
    Returns   : 1 if it succeeds
    Argument  : A pad ID and the public status you want to set : 1 or 0
    See       : http://etherpad.org/doc/v1.5.6/#index_setpublicstatus_padid_publicstatus

### get\_public\_status

    Usage     : $ec->get_public_status('padID')
    Purpose   : Return true of false
    Returns   : 1 or 0
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getpublicstatus_padid

### set\_password

    Usage     : $ec->set_password('padID', 'password')
    Purpose   : Returns ok or a error message
    Returns   : 1 if it succeeds
    Argument  : A pad ID and a password
    See       : http://etherpad.org/doc/v1.5.6/#index_setpassword_padid_password

### is\_password\_protected

    Usage     : $ec->is_password_protected('padID')
    Purpose   : Returns true or false
    Returns   : 1 or 0
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_ispasswordprotected_padid

### list\_authors\_of\_pad

    Usage     : $ec->list_authors_of_pad('padID')
    Purpose   : Returns an array of authors who contributed to this pad
    Returns   : An array or an array reference depending on the context, containing the epl authors IDs
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_listauthorsofpad_padid

### list\_names\_of\_authors\_of\_pad

    Usage     : $ec->list_names_authors_of_pad('padID')
    Returns   : Returns an array of the names of the authors who contributed to this pad in list context
                Returns an array reference in scalar context
    Argument  : The pad ID
    See       : This is not part of the Etherpad API but a facility offered by this module

### get\_last\_edited

    Usage     : $ec->get_last_edited('padID')
    Purpose   : Returns the timestamp of the last revision of the pad
    Returns   : A unix timestamp
    Argument  : A pad ID
    See       : http://etherpad.org/doc/v1.5.6/#index_getlastedited_padid

### send\_clients\_message

    Usage     : $ec->send_clients_message('padID', 'msg')
    Purpose   : Sends a custom message of type msg to the pad
    Returns   : 1 if it succeeds
    Argument  : A pad ID and the message you want to send
    See       : http://etherpad.org/doc/v1.5.6/#index_sendclientsmessage_padid_msg

## check\_token

    Usage     : $ec->check_token()
    Purpose   : Returns ok when the current api token is valid
    Returns   : 1 if the token is valid, 0 otherwise
    Argument  : None
    See       : http://etherpad.org/doc/v1.5.6/#index_checktoken

## Pads

See [http://etherpad.org/doc/v1.5.6/#index\_pads](http://etherpad.org/doc/v1.5.6/#index_pads)

### list\_all\_pads

    Usage     : $ec->list_all_pads()
    Purpose   : Lists all pads on this epl instance
    Returns   : An array or an array reference depending on the context, containing the pads names
    See       : http://etherpad.org/doc/v1.5.6/#index_listallpads

# BUGS and SUPPORT

You can find documentation for this module with the perldoc command.

    perldoc Etherpad

Bugs and feature requests will be tracked on:

    https://git.framasoft.org/luc/etherpad/issues

The latest source code can be browsed and fetched at:

    https://git.framasoft.org/luc/etherpad
    git clone https://git.framasoft.org/luc/etherpad.git

Source code mirror:

    https://github.com/ldidry/etherpad

You can also look for information at:

    AnnoCPAN: Annotated CPAN documentation

    http://annocpan.org/dist/Etherpad
    CPAN Ratings

    http://cpanratings.perl.org/d/Etherpad
    Search CPAN

    http://search.cpan.org/dist/Etherpad

# AUTHOR

    Luc DIDRY
    CPAN ID: LDIDRY
    ldidry@cpan.org
    https://fiat-tux.fr/

# COPYRIGHT

This program is free software; you can redistribute
it and/or modify it under the same terms as Perl itself.

The full text of the license can be found in the
LICENSE file included with this module.

# SEE ALSO

perl(1), [Mojolicious](https://metacpan.org/pod/Mojolicious)

# AUTHOR

Luc Didry <ldidry@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2015 by Luc Didry.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
