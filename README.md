# data-analysis
import pandas as pd
import os
from utils.scrape_helper import *
from utils.analysis_helper import *
        
# if the folder not in current directory..
def check_and_create_folder(folder_path):
    if not os.path.exists(folder_path):
        os.makedirs(folder_path)
    else:
        print(f"Folder '{folder_path}' already exists.")

# write the scraped article text to articles/ folder in form of text file
def write_article_to_text_file(file_location, article_text):
    with open(file_location, 'w', encoding='utf-8') as file:
        file.write(article_text)


if __name__ == '__main__':
    analysis_dic = {
        "URL_ID": [],
        "URL": [],
        "POSITIVE SCORE": [],
        "NEGATIVE SCORE": [],
        "POLARITY SCORE": [],
        "SUBJECTIVITY SCORE": [],
        "AVG SENTENCE LENGTH": [],
        "PERCENTAGE OF COMPLEX WORDS": [],
        "FOG INDEX": [],
        "AVG NUMBER OF WORDS PER SENTENCE": [],
        "COMPLEX WORD COUNT": [],
        "WORD COUNT": [],
        "SYLLABLE PER WORD": [],
        "PERSONAL PRONOUNS": [],
        "AVG WORD LENGTH": []
    }
    input_location = 'input/Input.xlsx'
    ouput_location = 'articles/'
            # if the articles folder not in current directory.. create it since we will store the articles text in that folder
    check_and_create_folder('articles/')
            # read the Input.xlsx from input directory
    input_df = pd.read_excel(input_location)
            # scrape the url from the input excel and store it as text file in articles folder
    co = 1
    for url in input_df['URL']:
        file_name = re.sub(r'[\\/*?:"<>|]', "_", url) + '.txt'
        output_file_location = ouput_location + file_name
        article_text = scrape_article(url)  # scrape the text for given url
        if not article_text:
            print('No response from website')
            co += 1
            continue
        write_article_to_text_file(output_file_location, article_text)  # write the scraped text to text file
        url_id = input_df.iloc[co-1]['URL_ID']
            # get the analysis result from analysis_helper and fill it to output data frame
        positive_score, negative_score, polarity_score, subjectivity_score = return_scores(article_text)
        avg_sentence_length, percentage_complex_words, fog_index, avg_words_per_sentence, complex_word_count, word_count, syllables_per_word, personal_pronouns, avg_word_length = return_readability_analysis(article_text) 
             # append values to each key in the dictionary
        analysis_dic['URL_ID'].append(url_id)
        analysis_dic['URL'].append(url)
        analysis_dic['POSITIVE SCORE'].append(positive_score)
        analysis_dic['NEGATIVE SCORE'].append(negative_score)
        analysis_dic['POLARITY SCORE'].append(polarity_score)
        analysis_dic['SUBJECTIVITY SCORE'].append(subjectivity_score)
        analysis_dic['AVG SENTENCE LENGTH'].append(avg_sentence_length)
        analysis_dic['PERCENTAGE OF COMPLEX WORDS'].append(percentage_complex_words)
        analysis_dic['FOG INDEX'].append(fog_index)
        analysis_dic['AVG NUMBER OF WORDS PER SENTENCE'].append(avg_words_per_sentence)
        analysis_dic['COMPLEX WORD COUNT'].append(complex_word_count)
        analysis_dic['WORD COUNT'].append(word_count)
        analysis_dic['SYLLABLE PER WORD'].append(syllables_per_word)
        analysis_dic['PERSONAL PRONOUNS'].append(personal_pronouns)
        analysis_dic['AVG WORD LENGTH'].append(avg_word_length)
        print('done link no.', co)
        print('==========================')
        co += 1
         # craeate a data frame for result of output
    output_df = pd.DataFrame(analysis_dic)
         # save the data frame as excel
    output_df.to_excel('analysis_output.xlsx', index=False)
