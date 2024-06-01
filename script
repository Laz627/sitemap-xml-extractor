import streamlit as st
import requests
import xml.etree.ElementTree as ET
import pandas as pd
from urllib.parse import urlparse

# Function to extract URLs from sitemap.xml
def extract_urls_from_sitemap(sitemap_url):
    response = requests.get(sitemap_url)
    if response.status_code != 200:
        raise Exception(f"Failed to retrieve sitemap: {response.status_code}")
    
    sitemap_content = response.content
    root = ET.fromstring(sitemap_content)
    namespace = {'ns': 'http://www.sitemaps.org/schemas/sitemap/0.9'}
    
    urls = []
    for url in root.findall('ns:url', namespace):
        loc = url.find('ns:loc', namespace).text
        urls.append(loc)
    
    return urls

# Function to categorize URLs based on subfolders
def categorize_urls(urls):
    categorized_data = {
        "Category": [],
        "Subcategory": [],
        "URL": []
    }
    
    for url in urls:
        path = urlparse(url).path
        subfolders = [part for part in path.split('/') if part]
        
        if len(subfolders) == 0:
            category = "shallow depth"
            subcategory = ""
        elif len(subfolders) == 1:
            category = subfolders[0]
            subcategory = ""
        else:
            category = subfolders[0]
            subcategory = subfolders[1]
        
        categorized_data["Category"].append(category)
        categorized_data["Subcategory"].append(subcategory)
        categorized_data["URL"].append(url)
    
    return categorized_data

# Function to save categorized URLs to an Excel file
def save_to_excel(categorized_data, output_file):
    df = pd.DataFrame(categorized_data)
    df.to_excel(output_file, index=False)

# Streamlit app
st.title("XML Sitemap Extractor Tool")

st.write("""
### Description
This tool allows you to extract URLs from a sitemap.xml file and categorize them based on the first two subfolders in the URL slug. You can download the categorized URLs as an Excel spreadsheet.

### How to Use the Tool
1. Enter the URL of your sitemap.xml file in the input box below.
2. Click the "Categorize URLs" button.
3. Once the URLs are processed, you can download the categorized results as an Excel file.
""")

sitemap_url = st.text_input("Enter the sitemap.xml URL")

if st.button("Categorize URLs"):
    if sitemap_url:
        try:
            urls = extract_urls_from_sitemap(sitemap_url)
            categorized_data = categorize_urls(urls)
            output_file = "categorized_urls.xlsx"
            save_to_excel(categorized_data, output_file)
            
            with open(output_file, "rb") as file:
                st.download_button(
                    label="Download categorized URLs",
                    data=file,
                    file_name="categorized_urls.xlsx",
                    mime="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
                )
                
            st.success("Categorized URLs saved successfully!")
        except Exception as e:
            st.error(f"Error: {e}")
    else:
        st.error("Please enter a valid sitemap.xml URL")
