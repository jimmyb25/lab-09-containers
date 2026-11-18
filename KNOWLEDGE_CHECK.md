# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

This is a bad idea because copying the .aws directory to the image would let anyone read your .aws credentials through the ghcr.io publicly available image, which would let them access your buckets, and also your mongodb credentials. Also, when the learner lab ends, people would have your credentials copied into their image. Those credentials would be invalid, and therefore everyone would be unable to run anything and I would also have to rebuild the image and republish to the ghcr.io every time I open a new learner lab session.

...

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

The get_atlas_settings function checked if there was the MONGODB_ATLAS_URL environment variable, to which the answer was False. Then, it ran the try-catch block, which creates a boto3 client that looks in the SSM Parameter Store to find a parameter called mongodb_credentials which contains our three credentials and then it returns that. AWS gave the credentials because we set up the docker run command with -v <.aws directory>:ro, which gave the container read only access to our credentials while it was running (did not upload to the image!)

...

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Before: eb976161bb3e

After: c976a60d9382

The fact that the old tag now references a different image is a huge problem because it doesn't allow us to use the old version. Just like the way we set package versions or python version, if the change introduced a bug somehow, we couldn't get the old version--also you wouldn't be able to tell based on the tag, so it could catch a user by surprise that things have changed. Instead, I should've named it with 0.1.1 or something else to indicate a new version.

...
