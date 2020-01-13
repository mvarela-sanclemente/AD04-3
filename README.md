
# Relación 1 a moitos
Vamos ver como creamos unha relación 1 a moitos.
No noso caso imos engadir ao noso equipo un array con xogadores.
Exemplo:
```java
import java.io.Serializable;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.Table;

@Entity
@Table(name="Xogador")
public class Xogador implements Serializable{
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private int id;
    @Column(name="nome")
    public String nome;
    @Column(name="dorsal")
    public int dorsal;
    @ManyToOne
    @JoinColumn(name="idEquipo")
    private Equipo equipo;
    
    public Xogador(){}
    
    public Xogador(String nome,int dorsal,Equipo equipo){
        this.nome = nome;
        this.dorsal = dorsal;
        this.equipo = equipo;
    }
    
}
```
Nesta clsse temos novo son as seguintes liñas:
- **@GeneratedValue(strategy=GenerationType.AUTO)**. Isto sirvenos para xerar un id automáticamente.
- **ManyToOne**. Estamos no lado de moitos a un (1 xogador pertenece a un equipo, pero cada equipo ten varios xogadores). Esto é o que indica anotación.
- **JoinColumm**: indicamos o nome da columna da táboa xogador que actuará como clave foránea do id de Equipo.

```java
import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;
import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.OneToMany;
import javax.persistence.OneToOne;
import javax.persistence.PrimaryKeyJoinColumn;
import javax.persistence.Table;

@Entity
@Table(name="Equipo")
public class Equipo implements Serializable{
    
    @Id
    @Column(name="id")
    private int id;
    @Column(name="nome")
    private String nome;
    @Column(name="cidade")
    private String cidade;
    @Column(name="numeroSocios")
    private int numeroSocios;
    
    @OneToMany(mappedBy = "equipo",cascade=CascadeType.ALL)
    private Set<Xogador> xogadores;
    
    @OneToOne(cascade= CascadeType.ALL)
    @PrimaryKeyJoinColumn
    private Entrenador entrenador;
    
    public void Equipo(){
    }
    
    public Equipo(int id,String nome, String cidade, int numeroSocios, Entrenador entrenador){
        this.id = id;
        this.nome = nome;
        this.cidade = cidade;
        this.numeroSocios = numeroSocios;
        this.entrenador = entrenador;
        this.xogadores = new HashSet<>();

    }
    
    public void addXogador(Xogador xogador){
        this.xogadores.add(xogador);
    }
    
}
```
Neta clase engadimos a clase Set para gardar todos os xogadores. E para ese atributo definimos as seguintes anotacións:
- **OneToMany**: esta anotación indica que para este atributo estamos do lado un a moitos.
- **mappedBy**: e o nome da columna da clase da relación moitos (neste caso xogadores) que actúa como clase foránea nesa clase.

> Recordade engadir a persistencia da clase Xogador a clase HibernateUtil.

```java
import org.hibernate.HibernateException;
import org.hibernate.Session;
import org.hibernate.Transaction;

public class Main {
    
    public static void main(String args[]){
        //Creamos un entrenador
        Entrenador entrenador = new Entrenador(1,"Nome1","Apelidos1",50);
        
        //Creamos un equipo
        Equipo equipo = new Equipo(1,"San Clemente", "Santiago", 1000, entrenador);
        Transaction tran = null;
        
        //Creamos os xogadores
        Xogador xogador1 = new Xogador("nome1",1,equipo);
        Xogador xogador2 = new Xogador("nome2",2,equipo);
        equipo.addXogador(xogador1);
        equipo.addXogador(xogador2);
        
        try{
            //Collemos a sesión de Hibernate
            Session session = HibernateUtil.getSessionFactory().openSession();
            //Comenzamos unha transacción
            tran = session.beginTransaction();
            
            //Gardamos o equipo
            session.save(equipo);
            
            //Facemos un commit da transacción
            tran.commit();
        }catch(HibernateException e){
            e.printStackTrace();
        }
    }
    
}
```
Aquí temos a clase Main para probar o que fixemos.
