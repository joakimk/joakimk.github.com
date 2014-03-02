---
layout: post
title: Only mock the things you own
tags: ruby code-design testing mocking solid
---

{{ page.title }}
====

Maybe you've heard people say that you should only mock the things you own. So what does that mean?

Say you mock something you don't own, like `Net::FTP`:

    expect(ftp).to receive(:getbinaryfile).with("name.jpg", "local/name.jpg", 1024)

What do you gain? A simulated behavior to make the test pass. A brittle test that gives no assurance that the code actually works at all. At worst even a test that says that the integration is correct even though it isn't.

So what is the alternative? Use mocks to define the API you need, then provide that API in an adapter, which will be the only part of your code that is coupled to the external library.

So instead of doing this:

    expect(ftp).to receive(:getbinaryfile).with("name.jpg", "local/name.jpg", 1024)

Do this:

    expect(client).to receive(:download_file).with("name.jpg", "local/")

And move the calls to Net::FTP into an FTP adapter, which you then test using integrated tests:

    it "can download a file" do
      test_server = make_ftp_server_with({ "remote/path/foo.jpg" => "image-data" })
      file = ImageFile.new("remote/path/foo.jpg")

      client = FtpClient.new(test_server)
      client.download_file(file, "tmp/test")

      expect(File.read("tmp/test/foo.jpg")).to eq("image-data")
    end

That way you don't mock the things you don't own while benefiting from using mocks to design the API you need.

## Why is it okay to mock the things you own?

When you've designed an API that is just big enough to get what you need done and no more, there is less of a risk to mock it. Infact, you probably arrived at that API by writing a mock passed into the code under test.

It's up to you to make sure that API and the code that depends on it does not drift apart. This can be done either by sheer will and displine (always grep for uses when changing) or by having some integration test coverage.

I tend to rely on a bit of both and it works good for me.

## So why use integrated tests for the FTP adapter?

If you don't and treat it like a white box by mocking the interactions you only know that the methods are called, not that they do the right thing.

I think integrated tests make sense in two places. At the edges of the system, testing adapters for external resources (http, database, ftp client, ...) and when going through the entire stack.

If you feel you need to use them in other places you should think twice about what makes you want to do that. Could the design be changed in such a way to have the parts integrating with an external service or third party library (like rails or gems) be separated from the code using that behavior?

## Design benefits

Maybe the application isn't dependent on FTP and could download files using other methods like HTTP or SCP? If you use mocks to define your own API you don't have to decide right away. By introducing seams (using dependency injection) into the code you can extend it without changing it in the future (open/closed principle) reducing the risk for bugs and reducing development time.

## Tradeoffs

If you're writing a really small application you can of course make other tradeoffs, but mocking things you don't own does not seem like a good idea in any case. In that case maybe just write an integrated test around the everything and let that be good enough.
