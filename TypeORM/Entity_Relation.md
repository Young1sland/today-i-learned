# 엔티티 생성 및 관계 설정
### 하기 조건을 충족하도록 설정해 본다.
> A는 B와 OneToOne 관계. B와 C는 OneToMany 관계.   
> B를 생성할 때 A,C도 같이 저장된다.
> B가 삭제되면 A도 삭제된다.   
> B가 삭제되면 C의 b_id 컬럼을 NULL로 설정한다.


### Entity A
```ts
@Entity('a_entity')
export class AEntity {
  @PrimaryGeneratedColumn({ name: 'id', unsigned: true })
  id: number;

  @OneToOne(() => BEntity, (b)=> b.AEntity, {
    onDelete: 'CASCADE', //B가 삭제되면 A도 삭제 됨
  })
  @JoinColumn([{ name: 'b_id', referencedColumnName: 'id' }])
  BEntity: BEntity; 
```


### B Entity
```ts
@Entity('b_entity')
export class BEntity {
  @PrimaryGeneratedColumn('int', { name: 'id', unsigned: true })
  id: number;

 //JoinColumn지정하지 않으면 컬럼 생성되지 않음.
  @OneToOne(() => AEntity, (aEntity) => aEntity.BEntity, {
    cascade: true, //B가 생성/업데이트/삭제 될 때 A도 같은 동작 발생
  })
  AEntity: AEntity;

//@RelationId로 해당 Entity의 id값만 프로퍼티에 저장할 수 있다.
  @RelationId((bEntity: BEntity) => BEntity.AEntity)
  aId: number;

  @OneToMany(() => CEntity, (c) => c.AEntity,{
    cascade: true, //B가 생성/업데이트/삭제될 때 C도 같은 동작 발생
  })
  CEntities: CEntity[];
}
```

### C Entity
```ts
@Entity('c_entity')
export class CEntity {
  @PrimaryGeneratedColumn({ name: 'id', unsigned: true })
  id: number;

  //JoinColumn으로만 정의하면 엔티티를 통해 해당 컬럼에 바로 접근할 수 없다. 이 경우 같은 컬럼을 따로 생성해주면 접근 가능하다.
  //FK로 설정된 JoinColumn과 같은 컬럼을 생성 시 nullable,default 제약조건을 넣어줘야 함.
  @Column('int', {
    name: 'b_id',
    unsigned: true,
    nullable: true,
    default: null,
  })
  bId: number | null;

  @ManyToOne(() => BEntity, (b) => b.CEntities, {
    onDelete: 'SET NULL', //B가 삭제되면 b_id 필드가 null로 설정 됨
  })
  @JoinColumn([{ name: 'b_id', referencedColumnName: 'id' }])
  BEntity: BEntity; 
}
```

### 소스
```ts
const queryRunner = this.connection.createQueryRunner();
await queryRunner.connect();
await queryRunner.startTransaction();
try {
  const cs: CEntity[] = [];
  const c1 = new CEntity();
  const c2 = new CEntity();
  cs.push(c1);
  cs.push(c2);

  const a = new AEntity();            

  const b = queryRunner.manager.getRepository(BEntity).create({
    AEntity: a,
    CEntities: cs,
  });
  await queryRunner.manager.getRepository(BEntity).save(b);
  //query를 보면 B가 insert된 후 A,C가 insert 됨
  await queryRunner.commitTransaction();
} catch (error) {
  await queryRunner.rollbackTransaction();
} finally {
  await queryRunner.release();
}
```
