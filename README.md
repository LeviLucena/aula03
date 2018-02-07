# aula03

package test.br.edu.impacta.ads;

import static org.junit.Assert.assertTrueFalse;

import org.junit.Before;
import org.junit.Test;

import br.edu.impacta.ads.Contato;
import br.edu.impacta.ads.ContatoDaoArquivo;

public class ContatoTest {

	ContatoDaoArquivo cdao;
	Contato contato;

	@Before
	public void setData(){
		cdao = new ContatoDaoArquivo("contatos.txt");
		contato = new Contato("", "123");
	}

	@Test
	public void testTest() {
		assertFalse("O contato não foi adicionado.", cdao.existe(contato));
	}
}
