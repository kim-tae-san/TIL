# Sqlalchemy

> sqlalchemy.dialect.postgresql.insert()

기본으로 제공하는 sqlalchemy.insert를 사용하지 못한 이유가 on_conflict_do_update를 사용할 수 없어서이다. postgresql에서 upsert를 개발하기 위해 dialect.insert를 이용한다