# Amazon S3 Vector

S3 Vector는 스토리지 서비스인 S3를 직접 사용하는 벡터 검색 기능으로서, 벡터 데이터를 저장하고 유사도 기반 검색을 수행할 수 있는 서비스입니다. 텍스트, 이미지, 오디오 등의 데이터를 임베딩하여 저장하고 검색할 수 있습니다. 이는 서브초 단위의 빠른 쿼리 응답 시간, 수백만 개의 벡터에 대한 검색 지원, 90% 이상의 평균 검색 정확도(recall) 제공, 메타데이터 필터링 기능 지원, 다른 AWS 서비스들과의 통합 지원의 장점을 가지고 있습니다.

S3 Vector는 의미론적 검색(Semantic Search), 추천 시스템, 이미지/텍스트 유사도 검색, AI 기반 검색 애플리케이션, 대규모 벡터 데이터베이스 구축에 장점이 있습니다.

## 활용 방법

QueryVectors API를 사용하여 유사도 검색을 수행할 수 있습니다.
검색 시 지정할 수 있는 주요 파라미터에는 쿼리 벡터, 반환할 결과 수(K-최근접 이웃), 인덱스 ARN, 메타데이터 필터(선택사항)가 있습니다.

```python
import boto3 
import json 

# Bedrock Runtime 및 S3 Vectors 클라이언트 생성
bedrock = boto3.client("bedrock-runtime", region_name="us-west-2")
s3vectors = boto3.client("s3vectors", region_name="us-west-2") 

# 쿼리 텍스트를 임베딩으로 변환
input_text = "adventures in space"
response = bedrock.invoke_model(
    modelId="amazon.titan-embed-text-v2:0",
    body=json.dumps({"inputText": input_text})
) 

# 임베딩 추출 및 벡터 검색
model_response = json.loads(response["body"].read())
embedding = model_response["embedding"]

# 벡터 인덱스 쿼리
response = s3vectors.query_vectors(
    vectorBucketName="media-embeddings",
    indexName="movies",
    queryVector={"float32": embedding}, 
    topK=3, 
    returnDistance=True,
    returnMetadata=True
)
```

## Reference

[Working with S3 Vectors and vector buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-vectors.html)

[boto3 - create_vector_bucket](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3vectors/client/create_vector_bucket.html)
