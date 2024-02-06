---
title: 'Amazon RDS MySQL 데이터를 Pandas DataFrame으로 가져오는 방법'
description: "Amazon RDS MySQL 데이터를 Pandas DataFrame으로 쉽게 가져오는 방법을 알아본다. Python, Pandas, SQLAlchemy를 활용하여 데이터베이스에서 데이터를 추출하고 분석하는 단계별 가이드를 제공한다."
date: '2024-02-02'
tags: [Amazon RDS, MySQL, 판다스(Pandas), 데이터프레임(DataFrame)]
---

# 1. 서론

![Amazon RDS MySQL 데이터를 Pandas DataFrame으로 가져오는 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/rds-mysql-to-pandas-dataframe.webp)

데이터베이스에서 정보를 추출하고 Python의 데이터 처리 라이브러리인 Pandas를 사용하여 분석하는 것은 일반적인 작업이다. Amazon Relational Database Service(RDS)에 호스팅된 MySQL 데이터베이스에서 데이터를 쿼리하고, 그 결과를 Pandas DataFrame으로 변환하는 과정을 자세히 아카이브한다.

# 2. 필요한 도구 및 라이브러리

- **Python**: 데이터 처리 및 분석을 위한 프로그래밍 언어이다.
- **Pandas**: 데이터 분석을 위한 Python 라이브러리로, 효율적인 데이터 구조와 데이터 분석 도구를 제공한다.
- **SQLAlchemy**: Python SQL 툴킷 및 객체 관계 매핑(ORM) 라이브러리로, 데이터베이스와의 상호 작용을 간소화한다.
- **MySQL Connector**: Python과 MySQL을 연결해주는 라이브러리이다. `pymysql` 또는 `mysql-connector-python`을 사용할 수 있다.

# 3. 설치 과정

먼저, 필요한 라이브러리를 설치해야 한다. Python의 패키지 관리자인 pip를 사용하여 쉽게 설치할 수 있다.

```sh
$ pip install pandas sqlalchemy pymysql
```
- `pandas`는 데이터 분석을 위해,
- `sqlalchemy`는 데이터베이스 연결을 위해,
- `pymysql`은 MySQL 데이터베이스 드라이버로 사용된다.

# 4. 데이터베이스 연결 설정

Amazon RDS MySQL 데이터베이스에 연결하기 위해선 데이터베이스의 호스트 주소, 포트 번호, 사용자 이름, 비밀번호, 그리고 데이터베이스 이름이 필요하다. SQLAlchemy 엔진을 생성하여 이러한 정보를 관리할 수 있다.

```py
from sqlalchemy import create_engine

host = "your-rds-hostname"
port = 3306
username = "your-username"
password = "your-password"
database = "your-database"

engine = create_engine(f"mysql+pymysql://{username}:{password}@{host}:{port}/{database}")
```


# 5. 데이터 쿼리 및 DataFrame 변환

이제 SQL 쿼리를 작성하여 데이터를 추출할 수 있다. `pandas의` `read_sql` 함수를 사용하면 SQL 쿼리의 결과를 직접 Pandas DataFrame으로 변환할 수 있다.

```py
import pandas as pd

query = "SELECT * FROM your_table"
df = pd.read_sql(query, engine)

print(df)
```

이 코드는 지정된 쿼리를 실행하고, 결과를 DataFrame 객체로 변환한다. `your_table`은 쿼리하려는 테이블 이름으로 대체해야 한다.

# 6. 전체 코드

- [GitHub Gist: 6mini/mysql_to_pandas.py](https://gist.github.com/6mini/10eca0855b344270c4e3aeb25a23519c)

```py
import pandas as pd
from sqlalchemy import create_engine

def query_to_dataframe(host, port, username, password, database, query):
    """
    주어진 SQL 쿼리를 실행하고 결과를 pandas DataFrame으로 반환한다.
    매개변수:
    host (str): 데이터베이스 서버 호스트 주소
    port (int): 데이터베이스 서버 포트 번호
    username (str): 데이터베이스 사용자 이름
    password (str): 데이터베이스 사용자 비밀번호
    database (str): 데이터베이스 이름
    query (str): 실행할 SQL 쿼리
    반환값:
    pandas.DataFrame: 쿼리 결과를 담은 DataFrame
    """
    # SQLAlchemy 엔진을 사용하여 데이터베이스에 연결
    engine = create_engine(f"mysql+pymysql://{username}:{password}@{host}:{port}/{database}")

    # SQL 쿼리 실행 및 결과 반환
    df = pd.read_sql(query, engine)
    return df

# 사용 예시
host = "your-rds-hostname"
port = 3306
username = "your-username"
password = "your-password"
database = "your-database"
query = "SELECT * FROM your_table"

df = query_to_dataframe(host, port, username, password, database, query)
print(df)
```

# 7. 마무리

Amazon RDS MySQL 데이터베이스에서 데이터를 추출하고 Pandas DataFrame으로 변환하는 과정을 소개했다. 실제 환경에서는 보안을 위해 자격 증명을 코드에 직접 포함시키지 않는 것이 중요하다. 환경 변수, AWS Secrets Manager 또는 비슷한 서비스를 통해 자격 증명을 안전하게 관리하는 것을 권장한다.