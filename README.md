@Entity
public class Topico {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String titulo;
    private String mensagem;
    private LocalDateTime dataCriacao;
    private String autor;

    // Getters e Setters
}
@Repository
public interface TopicoRepository extends JpaRepository<Topico, Long> {
}
@Service
public class TopicoService {
    @Autowired
    private TopicoRepository topicoRepository;

    public Topico criarTopico(Topico topico) {
        topico.setDataCriacao(LocalDateTime.now());
        return topicoRepository.save(topico);
    }

    public List<Topico> listarTopicos() {
        return topicoRepository.findAll();
    }

    public Topico encontrarTopicoPorId(Long id) {
        return topicoRepository.findById(id).orElseThrow(() -> new RuntimeException("Tópico não encontrado"));
    }

    public Topico atualizarTopico(Long id, Topico topicoAtualizado) {
        Topico topico = encontrarTopicoPorId(id);
        topico.setTitulo(topicoAtualizado.getTitulo());
        topico.setMensagem(topicoAtualizado.getMensagem());
        return topicoRepository.save(topico);
    }

    public void deletarTopico(Long id) {
        topicoRepository.deleteById(id);
    }
}
@RestController
@RequestMapping("/api/topicos")
public class TopicoController {
    @Autowired
    private TopicoService topicoService;

    @PostMapping
    public ResponseEntity<Topico> criarTopico(@RequestBody Topico topico) {
        Topico novoTopico = topicoService.criarTopico(topico);
        return ResponseEntity.status(HttpStatus.CREATED).body(novoTopico);
    }

    @GetMapping
    public ResponseEntity<List<Topico>> listarTopicos() {
        return ResponseEntity.ok(topicoService.listarTopicos());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Topico> encontrarTopicoPorId(@PathVariable Long id) {
        return ResponseEntity.ok(topicoService.encontrarTopicoPorId(id));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Topico> atualizarTopico(@PathVariable Long id, @RequestBody Topico topico) {
        return ResponseEntity.ok(topicoService.atualizarTopico(id, topico));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletarTopico(@PathVariable Long id) {
        topicoService.deletarTopico(id);
        return ResponseEntity.noContent().build();
    }
}
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/api/topicos").authenticated()
            .anyRequest().permitAll()
            .and()
            .httpBasic();
    }
}
