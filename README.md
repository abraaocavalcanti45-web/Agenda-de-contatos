# Agenda-de-contatos
Agenda de contatos
O banco de dados ficou com Abraão e mais da metade do replit 
o resto do replit e a forma visul do replit com Arthur vinicius 
e raphael junto com Arthur kleber e Arthur tavares revisão e aperfeiçoamento da forma visual 

o codico parte 1:
CREATE TABLE Contatos (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    telefone VARCHAR(20),
    endereco TEXT,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Parte 2:
 public List<Contatos> list() throws SQLException {
        String sql = "SELECT * FROM Contatos";
        Connection conn = Database.getConnection();
        Statement st = conn.createStatement();
        ResultSet rs = st.executeQuery(sql);

        List<Contatos> lista = new ArrayList<>();

        while (rs.next()) {
            Contatos c = new Contatos();
            c.setId(rs.getInt("id"));
            c.setNome(rs.getString("nome"));
            c.setEmail(rs.getString("email"));
            c.setTelefone(rs.getString("telefone"));
            c.setEndereco(rs.getString("endereco"));
            lista.add(c);
        }

        rs.close();
        st.close();
        conn.close();

        return lista;
    }
}

package br.edu.projeto.database;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class Database {
    public static Connection getConnection() throws SQLException {
        String url = System.getenv(jdbc:postgresql://ep-cold-tree-a4fehlqy-pooler.us-east-1.aws.neon.tech/neondb?sslmode=require
);
        String user = System.getenv(neondb_owner
);
        String pass = System.getenv(npg_oOMjz10GxNhr
);

        return DriverManager.getConnection(url, user, pass);
    }
}
package br.edu.projeto;

import br.edu.projeto.view.View;

public class Main {
    public static void main(String[] args) {
        new View().menu();
    }
}
package br.edu.projeto.model;

public class Contatos {
    private Integer id;
    private String nome;
    private String email;
    private String telefone;
    private String endereco;

    public Contatos() {}

    public Contatos(String nome, String email, String telefone, String endereco) {
        this.nome = nome;
        this.email = email;
        this.telefone = telefone;
        this.endereco = endereco;
    }

    public Integer getId() { return id; }
    public void setId(Integer id) { this.id = id; }

    public String getNome() { return nome; }
    public void setNome(String nome) { this.nome = nome; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }

    public String getTelefone() { return telefone; }
    public void setTelefone(String telefone) { this.telefone = telefone; }

    public String getEndereco() { return endereco; }
    public void setEndereco(String endereco) { this.endereco = endereco; }
}
package br.edu.projeto.view;

import br.edu.projeto.controller.ContatoController;
import br.edu.projeto.model.Contato;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.*;
import java.util.List;

public class AgendaContatosView extends JFrame {

    private JTextField txtNome, txtTelefone, txtEmail;
    private JTable tabela;
    private DefaultTableModel modelo;
    private ContatoController controller = new ContatoController();

    public AgendaContatosView() {
        setTitle("Agenda de Contatos");
        setSize(700, 500);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel painel = new JPanel();
        painel.setLayout(new BorderLayout());

        // 🔹 Painel dos campos
        JPanel painelCampos = new JPanel(new GridLayout(2, 4, 10, 10));

        painelCampos.add(new JLabel("Nome"));
        txtNome = new JTextField();
        painelCampos.add(txtNome);

        painelCampos.add(new JLabel("Telefone"));
        txtTelefone = new JTextField();
        painelCampos.add(txtTelefone);

        painelCampos.add(new JLabel("Email"));
        txtEmail = new JTextField();
        painelCampos.add(txtEmail);

        painel.add(painelCampos, BorderLayout.NORTH);

        // 🔹 Botões
        JPanel painelBotoes = new JPanel();

        JButton btnAdicionar = new JButton("Adicionar");
        JButton btnEditar = new JButton("Editar");
        JButton btnExcluir = new JButton("Excluir");

        painelBotoes.add(btnAdicionar);
        painelBotoes.add(btnEditar);
        painelBotoes.add(btnExcluir);

        painel.add(painelBotoes, BorderLayout.CENTER);

        // 🔹 Tabela
        modelo = new DefaultTableModel(new Object[]{"ID", "Nome", "Telefone", "Email"}, 0);
        tabela = new JTable(modelo);

        JScrollPane scroll = new JScrollPane(tabela);
        painel.add(scroll, BorderLayout.SOUTH);

        add(painel);

        carregarTabela();

        // 🔹 Ações dos botões
        btnAdicionar.addActionListener(e -> adicionarContato());
        btnEditar.addActionListener(e -> editarContato());
        btnExcluir.addActionListener(e -> excluirContato());
    }

    private void carregarTabela() {
        modelo.setRowCount(0); // limpar tabela
        List<Contato> lista = controller.listar();

        for (Contato c : lista) {
            modelo.addRow(new Object[]{c.getId(), c.getNome(), c.getTelefone(), c.getEmail()});
        }
    }

    private void adicionarContato() {
        Contato c = new Contato();
        c.setNome(txtNome.getText());
        c.setTelefone(txtTelefone.getText());
        c.setEmail(txtEmail.getText());

        controller.inserir(c);
        carregarTabela();
    }

    private void editarContato() {
        int linha = tabela.getSelectedRow();

        if (linha == -1) {
            JOptionPane.showMessageDialog(null, "Selecione um contato para editar.");
            return;
        }

        int id = (int) tabela.getValueAt(linha, 0);

        Contato c = new Contato();
        c.setId(id);
        c.setNome(txtNome.getText());
        c.setTelefone(txtTelefone.getText());
        c.setEmail(txtEmail.getText());

        controller.atualizar(c);
        carregarTabela();
    }

    private void excluirContato() {
        int linha = tabela.getSelectedRow();

        if (linha == -1) {
            JOptionPane.showMessageDialog(null, "Selecione um contato para excluir.");
            return;
        }

        int id = (int) tabela.getValueAt(linha, 0);
        controller.excluir(id);

        carregarTabela();
    }
}

