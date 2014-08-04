Lately I've been playing around with the GitHub API, and have been having a lot of fun doing it. The API is really full-featured, and lets you do almost anything that you could imagine doing with a real Git implementation. The problem with the API though, is that to do things like commit files, you have to make your way through a lot of low-level commands which can be confusing if you don't have a solid grasp of how Git works under the hood. Here we'll take a look at how to commit a file using the GitHub API, and learn a good bit about how Git works at the same time.

## Required reading

This post will be a lot easier to follow if you take a quick look at [this article which talks about how Git structures objects](http://git-scm.com/book/en/Git-Internals-Git-Objects). Pay special attention to Figure 9-3, shown here, which shows how Git objects are linked.

![Git object graph]({{ site.url }}/images/2014-08-08-commit-a-file-with-the-github-api/pro-git-9-3.jpg)

Don't worry if all this seems confusing at first. Walking through making a commit with the GitHub API will make everything a lot clearer. You'll just get a much better understanding if you take a quick look at that article (or at least check out the graph) beforehand.

## 1. Get a reference to HEAD

Let's get started. The first thing that you need to do is obtain a reference to your branch's `HEAD`. You can do this by making a request to the [get reference API endpoint](https://developer.github.com/v3/git/refs/#get-a-reference), substituting in `heads/[branchname]` for `:ref` in the API documentation. The API will respond with an object that gives you some information on the commit that your branch HEAD points to. Make note of both the SHA and the URL in the response object. We'll be needing those.

## 2. Grab the commit that HEAD points to

Take that URL that you grabbed a second ago and make a GET request to it. This will return the commit object that your branch HEAD points to. Commit objects, [as shown here](https://developer.github.com/v3/git/commits/#response), provide us with valuable information on the commit, including the SHA of the commit, and the SHA of the tree that the commit object points to. Note the commit SHA, the tree SHA, and the tree URL.

## 3. Post your new file to the server

Now it's time to actually start adding your new file. Start off by [posting it to the blobs API endpoint](https://developer.github.com/v3/git/blobs/#create-a-blob). Your payload must be in the following form.
	
{% highlight json %}
{
  "content": "Content of the blob"
  "encoding": "utf-8|base64"
}
{% endhighlight %}
  
As you can see, the content can be encoded in either UTF-8, or base 64. In response to your POST, GitHub will respond with information about the blob you uploaded. Take note of the SHA.

## 4. Get a hold of the tree that the commit points to

To get the tree, simply make a GET request to the tree URL retrieved in step 2. [The response](https://developer.github.com/v3/git/trees/#get-a-tree) will consist of some basic tree information and an array of objects that the tree contains. Make a note of the tree's SHA.

## 5. Create a tree containing your new file

The next step is to tell Git where in your repository your new file will live. To do this, you need to construct a tree containing the file(s) you wish to add. You can do this in two ways.

1. [Grab a full version of your latest Git tree](https://developer.github.com/v3/git/trees/#get-a-tree-recursively) by appending `?recursive=1` to the tree URL retrieved in step 2, and modify it.
2. Create a tree consisting only of the files that you wish to add, and give GitHub the SHA of the tree that you'd like to base your new tree on. It'll combine the two trees for you automatically. This is a really neat shortcut if you want to simply add or modify a few files. If you want to delete files, however, you'll have to revert to method #1 and publish an entire tree without a "base tree" specified.

Whatever method you choose, you'll need to make a call to the [create tree method on the API](https://developer.github.com/v3/git/trees/#create-a-tree) to actually create the tree on the server. The POST payload for a single-file addition using method #2 looks like this.

{% highlight json %}
{
  "base_tree": "",	// SHA of the base tree
  "tree": [
    {
      "path": "",	// Path to the file
      "mode": "",	// One of 100644 (blob), 100755 (executable),
					// 040000 (subdirectory/tree), 160000 (submodule/commit),
					// or 120000 (blob specifying path of symlink).
      "type": "",	// One of "blob", "tree", or "commit".
      "sha": ""		// SHA of the object
    }
  ]
}
{% endhighlight %}

To add more files to the commit, simply include them in the `tree` array. The format of `tree` is exactly the same as the trees returned by the API, so if in doubt regarding syntax, copy what GitHub sends you when you request a tree from a repository.

If instead you want to use method #1 and post an entirely new tree, simply remove the `base_tree` field and make sure that *all* files that you want in your repository are listed.

In response to your POST, the API will essentially send back the data that you sent to it, but will give you a SHA value for the tree that you have just created. Make note of this value.

## 6. Create a new commit

With the tree posted to the server, there is very little left to do. You now have to [create a commit which references your new tree](https://developer.github.com/v3/git/commits/#create-a-commit). In addition to this, you need to include the SHA(s) of the parent commit(s), as well as a commit message and a small amount of metadata. The entire payload is as follows.

{% highlight json %}
{
  "message": "",	// Your commit message.
  "parents": [""],	// Array of SHAs. Usually contains just one SHA.
  "tree": ""		// SHA of the tree.
}
{% endhighlight %}

In our specific case you'll want to put the SHA of the commit that you retrieved in step #2 in the `parents` array, and the SHA of your newly-created tree from step #5 in the `tree` field.

The server will in turn respond by giving you the SHA of your new commit. Hang on to this.

## 7. Update HEAD

That's almost it! We have only one thing left to do. Now that your commit is on the server along with all relevant data, the final step in the process is to move the `HEAD` reference on your branch up to the new commit. Take the SHA that you saved in the last step and plug it into a request to the [update reference](https://developer.github.com/v3/git/refs/#update-a-reference) endpoint. The name of the reference that you'll have to specify in the request is `[branchname]/HEAD`.

Executing this command will move the `HEAD` reference to point to your newly-minted commit. You're done! Now anybody who pulls the repository will see your new changes taken into account.

---

If you're not a GitHub API power user, you might be surprised at how much work it takes just to commit a file using the API! It's true, it is a lot of work. However this procedure is extremely similar to how Git actually works under the covers whenever you type `git commit`. Even if you don't intend on spending too much time with the GitHub API, learning to commit a file is a very good exercise in basic Git internals and in my opinion is time well spent.