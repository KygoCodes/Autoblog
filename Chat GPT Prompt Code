import openai
import pandas as pd
import requests
import os
import base64
import time
from tqdm import tqdm

openai.api_key = 'PUT YOUR CHAT GPT API KEY HERE'  # Add your OpenAI API Key

df = pd.read_csv('input.csv')  # Assuming input CSV file name is 'input.csv'

# Initialize the blog data list
blog_data = []

# Initialize a counter
counter = 0

# Convert the DataFrame to a list of dictionaries for tqdm
rows = df.to_dict('records')

# Create a new dataframe to store the results
output_df = pd.DataFrame(columns=['URL Slug', 'Meta Title', 'Description', 'Blog Content'])

# Loop over each row with tqdm tracking progress
for index, row in enumerate(tqdm(rows, desc='Generating blog posts')):
    url_slug = row['URL Slug']
    meta_title = row['Meta Title']
    description = row['Description of Page']
   
    # Step 1: Generate a detailed essay outline
    conversation_outline = [
        {
            "role": "system",
            "content": 'You are an essay-writing assistant who creates detailed outlines for essays. You always write at least 15 points for each outline.',
        },
        {
            "role": "user",
            "content": f"Create an outline for an essay about {meta_title} with at least 15 titles.",
        },
    ]

    response_outline = openai.ChatCompletion.create(
        model="gpt-4",
        messages=conversation_outline,
        max_tokens=1024,
        temperature=0.2
    )

    essay_outline = response_outline['choices'][0]['message']['content']

      # Step 2: Generate a blog post based on the essay outline - You can change the entire prompt based on your topic in the "system" area.
    conversation = [
    {
        "role": "system",
        "content": f'Never mention essay. Write an article using the {essay_outline}. Internal links are vital to SEO. Please always include a maximum 5 ahref internal links contextually in the article not just at the end. NEVER USE PLACEHOLDERS. ALWAYS WRITE ALL THE ARTICLE IN FULL. Always include 5 internal links. Output in HTML. Write an article using {essay_outline} with 3 paragraphs per heading. Each heading of the essay should have at least one list or table (with a small black border, and border between the rows and columns) also. It will go onto wordpress so I dont need opening HTML tags. Never use the word crucial, complex, complexities, navigating. Make sure the text can be read by an 8th grader I do not want any big words or extreme legal jargon. Do not apologize, do not try to sound smarter than you are. Please make the title a H1 Header. ',},
    {
        "role": "user",
        "content": f"Never leave an article incomplete, always write the entire thing. Make sure all content is relevant to the article. Use a fun tone of voice. Always include at least 5 internal links. Each heading from the essay outline should have at least 3 paragraphs and a table or list After writing the article, under H2 and H3 headers create an FAQ section, followed by FAQPage schema opening and closing with <script> tags.",
    },
]

    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=conversation,
        max_tokens=6400,
        temperature=0.2
    )


    blog_content = response['choices'][0]['message']['content']
    
    # Save the information into a new row in the output dataframe
    output_df = output_df._append({'URL Slug': url_slug, 'Meta Title': meta_title, 'Description': description, 'Blog Content': blog_content,}, ignore_index=True)
    
    # After each blog post is written, it's immediately saved to 'output.csv'
    output_df.to_csv('output.csv', index=False)
   

    # After generating 2 blog posts, sleep for 5 minutes (300 seconds)
    if (index + 1) % 2 == 0:
        time.sleep(100)
