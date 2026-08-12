
Hashnode Blog Manager
A GitHub Action that automates the publishing and updating of blog posts on Hashnode directly from your GitHub repository. Perfect for teams and open source projects that want to maintain their technical blogs using familiar Git workflows.

How It Works
This action monitors changes to blog content in your repository and automatically publishes or updates them on Hashnode:

File Detection: When the action is triggered, it identifies changed Markdown (.md) and configuration (.json) files.
Smart Publishing: It automatically determines whether to create a new post or update an existing one.
State Management: The action maintains a mapping between your repository file paths and Hashnode post IDs, allowing seamless updates.
Security: All sensitive information is stored as encrypted GitHub secrets.
Setup Instructions
Prerequisites
A Hashnode account and publication
A GitHub repository where you'll store your blog content
GitHub Personal Access Token with repo scope
Step 1: Prepare your repository structure
Create a directory in your repository to store blog posts. Each blog post should have:

A Markdown file (.md) with your content
A JSON configuration file with the same name containing metadata
Example structure:

/blogs
  /my-first-post
    post.md
    config.json
Step 2: Create the configuration file
Each blog post needs a JSON configuration file with metadata .

Refer to https://apidocs.hashnode.com/?source=legacy-api-page#definition-PublishPostInput to more about configs we can add

For example:

{
  "title": "My Awesome Blog Post",
  "tags": [
    {
      "slug": "javascript",
      "name": "JavaScript"
    },
    {
      "slug": "programming",
      "name": "Programming"
    }
  ],
  "coverImageURL": "https://example.com/image.jpg",
  "isRepublished": false,
  "originalArticleURL": "",
  "subtitle": "A beginner's guide to something interesting"
}
Step 3: Set up GitHub Secrets
Add the following secrets to your GitHub repository:

HASHNODE_TOKEN: Your Hashnode API token
PUBLICATION_ID: Your Hashnode publication ID
API_GITHUB_TOKEN: A GitHub Personal Access Token with repo scope
BLOG_IDS: Leave this empty initially, the action will populate it
Step 4: Create your GitHub workflow
Create a file in .github/workflows/hashnode.yml:

name: "Hashnode Blog Manager"
on:
  push:
    branches:
      - main

jobs:
  publish-to-hashnode:
    runs-on: ubuntu-latest
    steps:
      - name: Run Hashnode Blog Manager
        uses: Mukund-Tandon/Hashnode-Blog-Manger@main
        with:
          blog_path: "blogs"
          hashnode_access_token: ${{ secrets.HASHNODE_TOKEN }}
          publication_id: ${{ secrets.PUBLICATION_ID }}
          github_api_token: ${{ secrets.API_GITHUB_TOKEN }}
          blogIds: '${{ secrets.BLOG_IDS }}'
Usage
Creating a new blog post
Create a new directory in your blog path (e.g., /blogs/new-post)
Add both a Markdown file and a JSON configuration file
Commit and push to your main branch
The action will automatically publish the post to Hashnode
Updating an existing post
Modify the Markdown or JSON file of an existing post
Commit and push to your main branch
The action will automatically update the post on Hashnode
How The Action Works Under The Hood
Initialization: The action sets up the environment and reads the necessary tokens and IDs.

File Processing:

It reads changed files from the path specified in the workflow
For each directory, it looks for a Markdown file and a JSON configuration file
It validates that both files exist and are in the same directory
State Checking:

The action reads the BLOG_IDS secret to determine if a post already exists
This mapping connects your repository file paths to Hashnode post IDs
Publishing or Updating:

For new posts: It creates a post via Hashnode's GraphQL API and stores the mapping
For existing posts: It updates the post using the stored ID
State Persistence:

The action updates the BLOG_IDS mapping and securely stores it as a GitHub secret
It encrypts this data using GitHub's public key API
Troubleshooting
Common Issues
Missing Files: Both the Markdown and JSON files are required for new posts. For updates, at least one of them must be present.

Invalid JSON: Ensure your configuration file contains valid JSON. Use a validator if needed.

Secret Access: Make sure the GitHub token has the correct permissions to update repository secrets.
