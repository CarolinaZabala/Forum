# Forumimport java.io.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Main {
    // REQUISITO: Paradigma Orientado a Objetos (Classe interna para o Evento)
    static class Evento {
        String nome, endereco, categoria, horario, descricao;
        Evento(String n, String e, String c, String h, String d) {
            nome = n; endereco = e; categoria = c; horario = h; descricao = d;
        }
    }

    public static void main(String[] args) throws Exception {
        Scanner s = new Scanner(System.in);
        List<Evento> lista = new ArrayList<>();
        List<String> agenda = new ArrayList<>();
        DateTimeFormatter fmt = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");

        // REQUISITO: Toda vez que abrir, carregar dados do arquivo events.data
        File f = new File("events.data");
        if (f.exists()) {
            BufferedReader r = new BufferedReader(new FileReader(f));
            String l;
            while ((l = r.readLine()) != null) {
                String[] p = l.split(";");
                lista.add(new Evento(p[0], p[1], p[2], p[3], p[4]));
            }
            r.close();
        }

        // REQUISITO: Espaço para cadastro do usuário (Mínimo 3 atributos)
        System.out.println("--- CADASTRO ---");
        System.out.print("Nome: "); String u1 = s.nextLine();
        System.out.print("Email: "); String u2 = s.nextLine();
        System.out.print("Cidade: "); String u3 = s.nextLine();

        int op = -1;
        while (op != 0) {
            System.out.println("\n1-Cadastrar | 2-Consultar Próximos | 3-Participar | 4-Minha Agenda/Cancelar | 5-Já Ocorreram | 0-Sair");
            System.out.print("Opção: ");
            op = Integer.parseInt(s.nextLine());

            if (op == 1) {
                // REQUISITO: Atributos obrigatórios (nome, endereço, categoria, horário, descrição)
                System.out.print("Nome: "); String n = s.nextLine();
                System.out.print("Endereço: "); String ed = s.nextLine();
                System.out.print("Categoria (Festa/Show/Esporte): "); String c = s.nextLine();
                System.out.print("Horário (dd/MM/yyyy HH:mm): "); String h = s.nextLine();
                System.out.print("Descrição: "); String d = s.nextLine();

                lista.add(new Evento(n, ed, c, h, d));

                // REQUISITO: Salvar informações no arquivo events.data
                PrintWriter w = new PrintWriter(new FileWriter("events.data"));
                for (Evento ev : lista) w.println(ev.nome+";"+ev.endereco+";"+ev.categoria+";"+ev.horario+";"+ev.descricao);
                w.close();
                System.out.println("✅ Salvo!");

            } else if (op == 2) {
                System.out.println("\n--- PRÓXIMOS EVENTOS ---");
                // REQUISITO: Ordenar por horário e informar se está ocorrendo agora (DateTime)
                lista.sort((e1, e2) -> LocalDateTime.parse(e1.horario, fmt).compareTo(LocalDateTime.parse(e2.horario, fmt)));
                for (Evento ev : lista) {
                    LocalDateTime dt = LocalDateTime.parse(ev.horario, fmt);
                    if (dt.isAfter(LocalDateTime.now())) {
                        String status = dt.isBefore(LocalDateTime.now().plusHours(2)) ? "OCORRENDO AGORA" : "FUTURO";
                        System.out.println(ev.nome + " | " + ev.horario + " (" + status + ")");
                    }
                }
            } else if (op == 3) {
                // REQUISITO: Decidir participar de um evento listado
                System.out.print("Nome do evento: ");
                agenda.add(s.nextLine());
                System.out.println("✅ Presença confirmada!");

            } else if (op == 4) {
                // REQUISITO: Visualizar presenças confirmadas e permitir cancelar
                System.out.println("Sua Agenda: " + agenda);
                System.out.print("Cancelar algum? Digite o nome ou 'n': ");
                String canc = s.nextLine();
                if (!canc.equals("n")) agenda.remove(canc);

            } else if (op == 5) {
                // REQUISITO: Informar os eventos que já ocorreram
                System.out.println("\n--- JÁ OCORRERAM ---");
                for (Evento ev : lista) {
                    if (LocalDateTime.parse(ev.horario, fmt).isBefore(LocalDateTime.now())) {
                        System.out.println("• " + ev.nome + " (" + ev.horario + ")");
                    }
                }
            }
        }
    }
}