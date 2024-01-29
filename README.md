# API_Logradouros_MG
Api com todas os logradouros do estado de Minas Gerais (Em Desenvolvimento).

Segue um exemplo de implementação utilizando o React + Typescript para receber sugestoes conforme preenche um campo.

interface FormData {
   logradouro: string;
}

const SeuComponente: FC<ModalGerarChamadoProps> = () => {
   const initialFormData: FormData = {
      logradouro: "",
   };

   // autocomplete
   const [logradouros, setLogradouros] = useState<string[]>([]);
   const [sugestoesFiltradas, setSugestoesFiltradas] = useState<string[]>([]);

   // buscar logradouros api
   useEffect(() => {
      const fetchLogradouros = async () => {
         if (formData.logradouro.length >= 5) {
            try {
               // Array de caminhos para os dados JSON
               const files = ['https://raw.githubusercontent.com/marcosandradetf/API_Logradouros_MG/main/bh_logradouros.json', 'https://raw.githubusercontent.com/marcosandradetf/API_Logradouros_MG/main/neves_logradouros.json'];

               // Carrega todos os arquivos JSON simultaneamente
               const responses = await Promise.all(files.map(file => fetch(file)));
               const jsons = await Promise.all(responses.map(response => response.json()));

               // Combina os arrays de endereços em um único array
               const combinedLogradouros = jsons.reduce((accumulator, current) => [...accumulator, ...current], []);

               // Remove duplicatas usando um Set
               const dadosUnicos: string[] = Array.from(new Set(combinedLogradouros)) as string[];

               setLogradouros(dadosUnicos);

               // setando os dados em cache
               const sugestoesFiltradas = logradouros.filter(item => item.toLowerCase().includes(formData.logradouro.toLowerCase()));
               setSugestoesFiltradas(sugestoesFiltradas);
            } catch (error) {
               console.error('Erro ao carregar endereços:', error);
            }
         }
      };

      // Verifica se inputValue tem pelo menos 3 letras antes de fazer a busca
      if (formData.logradouro.length >= 5) {
         fetchLogradouros();
      } else {
         // Se o valor foi apagado, limpa o estado de logradouros
         setLogradouros([]);
         setSugestoesFiltradas([]);
      }
   }, [formData.logradouro]); // Adiciona inputValue como dependência para que o useEffect seja chamado quando inputValue mudar

   // ao digitar
   const handleInputChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
      const { name, value } = e.target;

      setFormData((prevData) => ({
         ...prevData,
         [name]: value,
      }));
   };

   // ao clicar na opcao de logradouro sugerida
   const handleOptionClick = (sugestao: string) => {
      // Atualiza o valor do input e limpa a lista de sugestões
      formData.logradouro = sugestao;
      setLogradouros([]);
      setSugestoesFiltradas([]);
   };

  return (
       <div className='d-flex'>
                     <label className='form-label'>
                        Logradouro:
                        <input type="text"
                           name='logradouro'
                           id="logradouro"
                           value={formData.logradouro}
                           list='sugestoes'
                           onChange={handleInputChange} className='form-control modalGerarLog' />
                        <ul className={`lista-autocomplete-logr form-control ${sugestoesFiltradas.length == 0 ? 'd-none' : ''} `}>
                           {sugestoesFiltradas.map((sugestao, index) => (
                              <li key={index} onClick={() => handleOptionClick(sugestao)} >
                                 {sugestao}
                              </li>
                           ))}
                        </ul>

                     </label>
                     <label className='form-label'>
                        Numeral:
                        <input type="number" name='numeral'
                           value={formData.numeral}
                           onChange={handleInputChange} className='form-control modalGerarNum' />
                     </label>
                  </div>
    );

};
      
