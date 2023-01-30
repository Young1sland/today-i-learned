## Migration
- DB 생성 후 테이블 수정이 필요할 경우 sql로 수정한 다음 typeorm entity를 수정해도 되지만 migration을 사용하는 것도 가능하다.
  
### Migration 명령어
```json
//package.json에 추가해 두자.
//migration file 생성, -n : file name
"db:create-migration": "npm run typeorm migration:create -- -n change-category ./src/migrations/",

//migration 실행
"db:migrate": "npm run typeorm migration:run",

//migration revert
"db:migrate:revert": "npm run typeorm migration:revert"

//migration generate, Entity 변경사항 체크하여 migration 내용 자동 생성
"db:generate-migration": "npm run typeorm migration:generate -- ./src/migrations -d ./ormconfig.ts"
```
1. migration file 생성
2. 생성한 file에 query 작성
```ts
export class changeCategory1675075582837 implements MigrationInterface {
    //변경 사항 작성
    public async up(queryRunner: QueryRunner): Promise<void> {
    }

    //Rollback 필요할 경우
    public async down(queryRunner: QueryRunner): Promise<void> {
    }
}
```
3. migration 실행
```shell
$ npm run db:migrate
```
4. revert 하기
```shell
$ npm run db:migrate:revert
```


