---
layout: post
title: "Making friends"
description: "store and query directed pairs of objects"
category: examples 
tags: [matlab,example,normal form]
---
{% include JB/setup %}
This <a href="http://www.reddit.com/r/mysql/comments/20kc8s/is_there_an_efficient_way_to_find_all_pairs_of/">post</a> asks to make an efficient SQL query for matching pairs of rows.  Here I show how it would be done in DataJoint for MATLAB.

Let table **users.Friends** be defined (in MATLAB) as follows:

{% highlight Matlab %}
%{
users.Friends (manual)  # friendships 
user1 :  int  #  user inviting friendship
user2 :  int  #  user accepting friendship
%}
classdef Friends < dj.Relvar
end
{% endhighlight %}

A friend "invite" from 432 to user 876 would create a new row:

{% highlight Matlab %}
insert(users.Friends, struct('user1',432,'user2',876))
{% endhighlight %}

A friend "accept" would create another row with user values swapped:

{% highlight Matlab %}
insert(users.Friends, struct('user1',876,'user2',432))
{% endhighlight %}

So a friend relationship consist of two rows in <code>users.Friends</code>.

How does one lookup all friends of user 432?

With this design, this query can be done so:

{% highlight Matlab %}
res = (users.Friends & 'user1=432')*...
    pro(users.Friends,'user1->user2','user2->user1');
{% endhighlight %}

Compare this to equivalent SQL code:

{% highlight SQL %}
SELECT
    *
FROM
    friends f1 join
    friends f2 on f1.user_1=f2.user_2 and f1.user_2=f2.user_1
WHERE
    f1.user_1=432 
{% endhighlight %}


However, the provided design did not conform to DataJoint's best practices for data regularization. In a DataJoint database, objects are identified by the same primary key with the same attribute names everywhere. So a user would not be identified by <code>user1</code> in one place and <code>user2</code> in another.

In DataJoint, we would first create a user table

{% highlight Matlab %}
%{
users.User (manual)  # tables of users
user_id  : int  # universal user id
----
fullname   : varchar(255)  # user's name
%}
classdef User < dj.Relvar
end
{% endhighlight %}

Let's populate it with two records:

{% highlight Matlab %}
u = users.User;
u.insert(struct('user_id',876,'fullname','Romeo'))
u.insert(struct('user_id',432,'fullname','Juliet'))
{% endhighlight %}

Let's define the **Friend** table, which references **Users**

{% highlight Matlab %}
%{
users.Friends (manual) # friendship invites
invite_id  : int auto_increment   # friendship id
role = "inviter"      : enum("inviter","accepter")  # users' role in this invite
-----
->users.User
status = "requested"  : enum("requested","accepted")   # status
%}

classdef Friends < dj.Relvar
end
{% endhighlight %}

User 432 invites user 876 by making an "Accepted" tuple for himself and "Requested" tuple for his future friend:

{% highlight Matlab %}
inviter = 432;
invitee = 876;
f = users.Friends;
f.insert(struct('user_id', inviter,'role', 'inviter', 'status', 'accepted'))
f.insert(struct('invite_id', i.lastInsertID, 'user_id', invitee, 'role', 'accepter', 'status', 'requested'))
{% endhighlight %}

The invitee can check her unanswered requests as

{% highlight Matlab %}
unanswered = f & struct('user_id',invitee,'status','requested')
{% endhighlight %}

The invitee can accept all the unanswered requests by changing the status to "accepted":

{% highlight Matlab %}
replies = unanswered.fetch('*');
replies = arrayfun(@(k) setfield(k,'status','accepted'), replies);
f.insert(replies,'REPLACE')
{% endhighlight %}

Finally, a user can see her friends by getting pairs of **Friends** tuples in which both statuses are "accepted":

{% highlight Matlab %}
me = struct('user_id',432);    % who is asking
f = f & 'status="accepted"';  % accepted friendships
f1 = f.pro('user_id','role'); 
% prepare for join by renaming attributes in friends' tuples
f2 = f.pro('user_id->friend_id','role->friend_role');
% get friends' ids
friendIds = fetchn(f1*f2 & 'user_id<>friend_id' & me, 'friend_id')
{% endhighlight %}
