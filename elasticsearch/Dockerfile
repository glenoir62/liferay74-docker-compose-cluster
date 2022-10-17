#FROM docker.elastic.co/elasticsearch/elasticsearch-oss:6.1.3
#FROM elasticsearch:6.5.4

FROM elasticsearch:7.17.6

RUN bin/elasticsearch-plugin install analysis-kuromoji && \
	bin/elasticsearch-plugin install analysis-icu && \
	bin/elasticsearch-plugin install analysis-smartcn && \
	bin/elasticsearch-plugin install analysis-stempel

#COPY ./config ./config
