

## 1. Get a reference to HEAD

The first thing that you need to do is obtain a reference to your branch's `HEAD`. You can do this by making a request to the [get reference API endpoint](https://developer.github.com/v3/git/refs/#get-a-reference), substituting in `heads/branchname` for `:ref` in the API documentation. The API will respond with an object that gives you some information on the commit that your branch HEAD points to. Make note of both the SHA and the URL in the response object. We'll be needing those.

## 2. Grab the commit that HEAD points to

Remember that URL that you grabbed a second ago? The next step is to make a GET request to it, which will return a commit object. Commit objects, [as shown here](https://developer.github.com/v3/git/commits/#response), provide us with valuable information on the commit, including the SHA of the commit, and the SHA of the tree that the commit object points to. (If you're lost, scroll up and have another look at that diagram from the beginning of the post.)

We need both those SHAs to commit our new file.

Make a GET request to the URL returned in the last step. This will return a commit object, which looks something like the following.

{% highlight json %}
{
  "sha": "7638417db6d59f3c431d3e1f261cc637155684cd",
  "url": "https://api.github.com/repos/octocat/Hello-World/git/commits/7638417db6d59f3c431d3e1f261cc637155684cd",
  "author": {
    "date": "2010-04-10T14:10:01-07:00",
    "name": "Scott Chacon",
    "email": "schacon@gmail.com"
  },
  "committer": {
    "date": "2010-04-10T14:10:01-07:00",
    "name": "Scott Chacon",
    "email": "schacon@gmail.com"
  },
  "message": "added readme, because im a good github citizen\n",
  "tree": {
    "url": "https://api.github.com/repos/octocat/Hello-World/git/trees/691272480426f78a0138979dd3ce63b77f706feb",
    "sha": "691272480426f78a0138979dd3ce63b77f706feb"
  },
  "parents": [
    {
      "url": "https://api.github.com/repos/octocat/Hello-World/git/commits/1acc419d4d6a9ce985db7be48c6349a0475975b5",
      "sha": "1acc419d4d6a9ce985db7be48c6349a0475975b5"
    }
  ]
}
{% endhighlight %}

Make note of `tree.url` and `tree.sha`.

## 4. Post your new file to the server

Now it's time to actually start adding your new file to your repository. Start off by posting it to the server using the following address.

	POST /repos/:owner/:repo/git/blobs
	
The payload of your POST must contain the following data

{
  "content": "Content of the blob"
  "encoding": "utf-8|base64"
}
  
As you can see, the content can be encoded in either UTF-8, or base 64. In response to your POST, GitHub will respond with information about the blob you uploaded.

{
  "content": "Content of the blob",
  "encoding": "utf-8",
  "url": "https://api.github.com/repos/octocat/example/git/blobs/3a0f86fb8db8eea7ccbb9a95f325ddbedfb25e15",
  "sha": "3a0f86fb8db8eea7ccbb9a95f325ddbedfb25e15",
  "size": 100
}

As usual, take note of the `sha`.

## 3. Get a hold of the tree that the commit points to (maybe)

To get the tree, simply make a GET request to the URL retrieved in the last step. The response will be something along these lines.

{
  "sha": "9fb037999f264ba9a7fc6274d15fa3ae2ab98312",
  "url": "https://api.github.com/repos/octocat/Hello-World/trees/9fb037999f264ba9a7fc6274d15fa3ae2ab98312",
  "tree": [
    {
      "path": "file.rb",
      "mode": "100644",
      "type": "blob",
      "size": 30,
      "sha": "44b4fc6d56897b048c772eb4087f854f46256132",
      "url": "https://api.github.com/repos/octocat/Hello-World/git/blobs/44b4fc6d56897b048c772eb4087f854f46256132"
    },
    {
      "path": "subdir",
      "mode": "040000",
      "type": "tree",
      "sha": "f484d249c660418515fb01c2b9662073663c242e",
      "url": "https://api.github.com/repos/octocat/Hello-World/git/blobs/f484d249c660418515fb01c2b9662073663c242e"
    },
    {
      "path": "exec_file",
      "mode": "100755",
      "type": "blob",
      "size": 75,
      "sha": "45b983be36b73c0788dc9cbcb76cbb80fc7bb057",
      "url": "https://api.github.com/repos/octocat/Hello-World/git/blobs/45b983be36b73c0788dc9cbcb76cbb80fc7bb057"
    }
  ]
}

As in the previous steps, make note of `sha`.

## 5. Create a tree containing your new file

The next step is to tell Git where in your repository your new file will live. To do this, you need to construct a tree containing the file(s) you wish to add. You can do this in two ways.

1. Grab the tree that you retrieved in step #3 and modify it.
2. Create a tree consisting only of the files that you wish to add, and give GitHub the SHA of the tree that you'd like to base your new tree on. It'll combine the two trees for you automatically. This method is a neat shortcut if you want to simply add or modify a few files, but if you want to delete files you'll have to revert to method #1 and publish an entire tree without a "base tree" specified.

Whatever method you choose, you'll need to make a call to the create tree method on the API to actually create the tree on the server.

	POST /repos/:owner/:repo/git/trees
	
The POST payload for a single-file addition using method #2 looks like this.

{
  "base_tree": "9fb037999f264ba9a7fc6274d15fa3ae2ab98312",
  "tree": [
    {
      "path": "",
      "mode": "", // One of 100644 (blob), 100755 (executable),
				  // 040000 (subdirectory/tree), 160000 (submodule/commit),
				  // or 120000 (blob specifying path of symlink).
      "type": "", // One of "blob", "tree", or "commit".
      "sha": ""
    }
  ]
}

To add more files to the commit, simply include them in the `tree` array. The format of `tree` is exactly the same as the trees returned by the API, so if in doubt regarding syntax, copy what GitHub sends you when you request a tree from a repository.

If instead you want to use method #1 and post an entirely new tree, simply remove the `base_tree` field and make sure that *all* files that you want in your repository are listed.

In response to your POST, the API will essentially send back the data that you sent to it.

{
  "sha": "cd8274d15fa3ae2ab983129fb037999f264ba9a7",
  "url": "https://api.github.com/repos/octocat/Hello-World/trees/cd8274d15fa3ae2ab983129fb037999f264ba9a7",
  "tree": [
    {
      "path": "file.rb",
      "mode": "100644",
      "type": "blob",
      "size": 132,
      "sha": "7c258a9869f33c1e1e1f74fbb32f07c86cb5a75b",
      "url": "https://api.github.com/repos/octocat/Hello-World/git/blobs/7c258a9869f33c1e1e1f74fbb32f07c86cb5a75b"
    }
  ]
}

Once again, take note of the `sha`.

## 6. Create a new commit

With the tree posted to the server, there is very little left to do. You now have to create a commit which references your new tree. In addition to this, you need to include the SHA(s) of the parent commit(s), as well as a commit message and a small amount of metadata. The entire payload is as follows.

{
  "message": "my commit message",
  "author": {
    "name": "Scott Chacon",
    "email": "schacon@gmail.com",
    "date": "2008-07-09T16:13:30+12:00"
  },
  "parents": [
    "7d1b31e74ee336d15cbd21741bc88a537ed063a0"
  ],
  "tree": "827efc6d56897b048c772eb4087f854f46256132"
}

So, in our specific case you'll want to put the SHA of the commit that you retrieved in step #2 in the `parents` array, and the SHA of your newly-created tree from step #5 in the `tree` field. The rest needs no explanation.

The server will in turn respond by giving you the SHA of your new commit. Save this.

## 7. Update HEAD

This is it! Your commit is now on the server along with all relevant data. The final step in the process is to move the `HEAD` reference on your branch up to the new commit. Take the SHA that you saved in the last step and plug it into a request to the [update reference](https://developer.github.com/v3/git/refs/#update-a-reference) endpoint. The name of the reference that you'll have to specify in the request is ":branchname/HEAD".

Executing this command will move the `HEAD` reference to point to your newly-minted commit. You're done! Now anybody who pulls the repository will see your new changes taken into account.

---

If you're not a GitHub API power user (and if you're reading this, you probably aren't), you might be surprised at how much work it takes just to commit a file using the API! It's true, it is a lot of work. However this procedure is extremely similar to how Git actually works under the covers. Even if you don't intend on spending too much time with the GitHub API, learning to commit a file is a very good exercise in basic Git internals.