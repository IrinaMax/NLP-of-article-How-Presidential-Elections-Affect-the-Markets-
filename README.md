# NLP-of-article-How-Presidential-Elections-Affect-the-Markets-
Here I perfom analysis using Natural Language Processing of the article “How Presidential Elections Affect the Markets” from 
"Merill Lynch" https://www.ml.com/articles/how-presidential-elections-affect-the-markets.html
![screenshot 2016-11-29 20 13 47](https://cloud.githubusercontent.com/assets/16123495/20739573/a35b03b2-b670-11e6-8852-04613c8fc199.png)

 ## Entity Recognition/extraction, Visualising output

    require(rJava)
    require(NLP)
    require(openNLP)
    library(magrittr)

    text <- readLines("AE_Market.txt")
    print(text)
    text <- paste(text, collapse = " ")
    print(text)
    text <- as.String(text)
    text

## Chunking needs word token annotations with POS tags.
    sent_ann <- Maxent_Sent_Token_Annotator()
    word_ann <- Maxent_Word_Token_Annotator()
    pos_ann <- Maxent_POS_Tag_Annotator()

    pos_annotations <- annotate(text, list(sent_ann, word_ann, pos_ann))
    text_annotations <- annotate(text, list(sent_ann, word_ann))
    head(text_annotations)
           
    text_doc <- AnnotatedPlainTextDocument(text, text_annotations)         
    words(text_doc) %>% head(10)

    person_ann <- Maxent_Entity_Annotator(kind = "person")
    location_ann <- Maxent_Entity_Annotator(kind = "location")
    organization_ann <- Maxent_Entity_Annotator(kind = "organization")
    date_ann <- Maxent_Entity_Annotator(kind = "date")
    money_ann <- Maxent_Entity_Annotator(kind = "money")

## list of function to get this words
    pipeline <- list(sent_ann,
                 word_ann,
                 person_ann,
                 location_ann,
                 organization_ann,
                 date_ann,
                 money_ann)
    text_annotations <- annotate(text, pipeline)
    text_annotations
    text_doc <- AnnotatedPlainTextDocument(text, text_annotations)
    text_doc

    entities <- function(doc, kind) {
     s <- doc$content
     a <- annotations(doc)[[1]]
     if(hasArg(kind)) {
        k <- sapply(a$features, `[[`, "kind")
          s[a[k == kind]]
      } else {
        s[a[a$type == "entity"]]
      }
    }

    entities(text_doc, kind = "person")
    entities(text_doc, kind = "location")
    entities(text_doc, kind = "organization")
    entities(text_doc, kind = "date")
    entities(text_doc, kind = "money")


## Visualising googleVis
    library(googleVis)
    plot(table(entities(text_doc, kind = "person")) )
![rplot1](https://cloud.githubusercontent.com/assets/16123495/20739474/c36e42a0-b66f-11e6-9084-ba1fd89e56de.png)    

    df_AE1 <- data.frame(table(entities(text_doc, kind = "person")))
    df_AE_bar1 <- gvisColumnChart(df_AE1)
    plot(df_AE_bar1)
![screenshot r_nlp_entities_person](https://cloud.githubusercontent.com/assets/16123495/20739477/c7507bf4-b66f-11e6-868b-ef01b9612779.png)    

    df_AE2 <- data.frame(table(entities(text_doc, kind = "organization")))
    df_AE_bar2 <- gvisColumnChart(df_AE2)
    plot(df_AE_bar2)
![screenshot r_nlp_plot_with_entities_organization](https://cloud.githubusercontent.com/assets/16123495/20739478/c918ac0e-b66f-11e6-9ad4-a18eced0c16c.png)
    
    
