
# Proyecto final Backend
## Proyecto final Técnicas de Programación 2
En este repositorio adicionen todo lo que tenga que ver con el código que va a tener que ver con su API Rest y almacenamiento en base de datos.

package Model;

import java.util.ArrayList;
import java.util.List;

/**
 *
 * @author NECSOFT
 */
public class AgendaModelo {
    private List<Object> eventos;

	public AgendaModelo() {
		this.eventos = new ArrayList<>();
	}

	public void agregarEvento(EventoInterface evento) throws EventoDuplicadoException {
		if (buscarEventoPorNombre(evento.getNombre()) != null) {
			throw new EventoDuplicadoException("El evento con el nombre '" + evento.getNombre() + "' ya existe.");
		}
		eventos.add(evento);
	}

	public EventoInterface buscarEventoPorNombre(String nombre) {
		for (Object obj : eventos) {
			if (obj instanceof EventoInterface) {
				EventoInterface evento = (EventoInterface) obj;
				if (evento.getNombre().equalsIgnoreCase(nombre)) {
					return evento;
				}
			}
		}
		return null;
	}

	public void editarEventoNombre(EventoInterface evento, String nuevoNombre) {
		evento.setNombre(nuevoNombre);
	}

	public void editarEventoDescripcion(EventoInterface evento, String nuevaDescripcion) {
		evento.setDescripcion(nuevaDescripcion);
	}

	public List<Object> obtenerEventos() {
		return eventos;
	}

	public void eliminarEvento(EventoInterface evento) {
		eventos.remove(evento);
	}
    
}

package Model;

import java.time.LocalDate;
import java.time.format.DateTimeParseException;

/**
 *
 * @author NECSOFT
 */
public class Evento implements EventoInterface{
    	private String nombre;
	private String descripcion;
	private LocalDate fecha;

	public Evento(String nombre, String descripcion, String fecha) throws DateTimeParseException {
		this.nombre = nombre;
		this.descripcion = descripcion;
		this.fecha = LocalDate.parse(fecha);
	}

	@Override
	public String getNombre() {
		return nombre;
	}

	@Override
	public void setNombre(String nombre) {
		this.nombre = nombre;
	}

	@Override
	public String getDescripcion() {
		return descripcion;
	}

	@Override
	public void setDescripcion(String descripcion) {
		this.descripcion = descripcion;
	}

	@Override
	public String getFecha() {
		return fecha.toString();
	}

	@Override
	public void setFecha(String fecha) {
		this.fecha = LocalDate.parse(fecha);
	}

	@Override
	public long getDiasFaltantes() {
		return LocalDate.now().until(fecha).getDays();
	}

	@Override
	public String toString() {
		return "Evento{" + "nombre='" + nombre + '\'' + ", descripcion='" + descripcion + '\'' + ", fecha=" + fecha
				+ ", dÃ­as faltantes=" + getDiasFaltantes() + '}';
	}
}

package Model;

/**
 *
 * @author NECSOFT
 */
public class EventoDuplicadoException extends Exception {
    	public EventoDuplicadoException(String message) {
		super(message);
	}
    
}
package Model;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 *
 * @author NECSOFT
 */
public class EventoFactory {
    public static EventoInterface crearEvento(String tipoEvento, String nombre, String descripcion, String fecha,
			String participantes, String lugar, String recordatorios) {
		switch (tipoEvento.toLowerCase()) {
		case "tarea":
			return new Tarea(nombre, descripcion, fecha, false);
		case "reuniÃ³n":
			List<String> participantesList = new ArrayList<>();
			if (participantes != null && !participantes.isEmpty()) {
				participantesList = Arrays.asList(participantes.split(","));
			}
			return new Reunion(nombre, descripcion, fecha, lugar, participantesList);
		case "recordatorio":
			List<String> recordatoriosList = new ArrayList<>();
			if (recordatorios != null && !recordatorios.isEmpty()) {
				recordatoriosList = Arrays.asList(recordatorios.split(","));
			}
			return new Recordatorio(nombre, descripcion, fecha, recordatoriosList);
		default:
			throw new IllegalArgumentException("Tipo de evento no vÃ¡lido: " + tipoEvento);
		}
	}
}
package Model;

/**
 *
 * @author NECSOFT
 */
public interface EventoInterface {
    	String getNombre();

	void setNombre(String nombre);

	String getDescripcion();

	void setDescripcion(String descripcion);

	String getFecha();

	void setFecha(String fecha);

	long getDiasFaltantes();
}
public class EventoNoEncontradoException extends Exception{
    public EventoNoEncontradoException(String message) {
		super(message);
	}
}
package Model;

/**
 *
 * @author NECSOFT
 */
public class FechaInvalidaException extends Exception {
    public FechaInvalidaException(String message) {
		super(message);
	}
}
package Model;

import java.util.List;

/**
 *
 * @author NECSOFT
 */
public class Recordatorio extends Evento {
    private List<String> recordatorios;

	public Recordatorio(String nombre, String descripcion, String fecha, List<String> recordatorios) {
		super(nombre, descripcion, fecha);
		this.recordatorios = recordatorios;
	}

	public List<String> getRecordatorios() {
		return recordatorios;
	}

	public void setRecordatorios(List<String> recordatorios) {
		this.recordatorios = recordatorios;
	}

	@Override
	public String toString() {
		return super.toString() + " Recordatorio{" + "recordatorios=" + recordatorios + '}';
	}
}
package Model;

/**
 *
 * @author NECSOFT
 */
public class Tarea extends Evento {
    private boolean completada;

	public Tarea(String nombre, String descripcion, String fecha, boolean completada) {
		super(nombre, descripcion, fecha);
		this.completada = completada;
	}

	public boolean isCompletada() {
		return completada;
	}

	public void setCompletada(boolean completada) {
		this.completada = completada;
	}

	@Override
	public String toString() {
		return super.toString() + " Tarea{" + " completada=" + completada + '}';
	}
}
package Model;

/**
 *
 * @author NECSOFT
 */
public class TipoEventoInvalidoException extends Exception{
     public TipoEventoInvalidoException(String message) {
        super(message);
    }
}
package Presentacion;

import Model.EventoInterface;
import java.awt.BorderLayout;
import java.awt.GridLayout;
import java.util.List;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.JTextField;
import proyectofinalagenda.AgendaControlador;

/**
 *
 * @author NECSOFT
 */
public class AgendaVista extends JFrame{
    private AgendaControlador controlador;
	private JTextArea textArea;
	private JTextField nombreField;
	private JTextField descripcionField;
	private JTextField fechaField;
	private JTextField tipoField;
	private JTextField participantesField;
	private JTextField lugarField;
	private JTextField recordatoriosField;

	public AgendaVista() {
		inicializarComponentes();
	}

	private void inicializarComponentes() {
		setTitle("Agenda");
		setSize(800, 600);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		setLayout(new BorderLayout());

		textArea = new JTextArea();
		add(new JScrollPane(textArea), BorderLayout.CENTER);

		JPanel inputPanel = crearPanelDeEntrada();
		add(inputPanel, BorderLayout.NORTH);

		setVisible(true);
	}

	private JPanel crearPanelDeEntrada() {
		JPanel inputPanel = new JPanel(new GridLayout(0, 2));
		inputPanel.add(new JLabel("Nombre:"));
		nombreField = new JTextField();
		inputPanel.add(nombreField);

		inputPanel.add(new JLabel("Descripción:"));
		descripcionField = new JTextField();
		inputPanel.add(descripcionField);

		inputPanel.add(new JLabel("Fecha (yyyy-MM-dd):"));
		fechaField = new JTextField();
		inputPanel.add(fechaField);

		inputPanel.add(new JLabel("Tipo de Evento (tarea, reunión, recordatorio):"));
		tipoField = new JTextField();
		inputPanel.add(tipoField);

		inputPanel.add(new JLabel("Participantes (separados por comas):"));
		participantesField = new JTextField();
		inputPanel.add(participantesField);

		inputPanel.add(new JLabel("Lugar:"));
		lugarField = new JTextField();
		inputPanel.add(lugarField);

		inputPanel.add(new JLabel("Recordatorios (separados por comas):"));
		recordatoriosField = new JTextField();
		inputPanel.add(recordatoriosField);

		JButton agregarButton = new JButton("Agregar Evento");
		agregarButton.addActionListener(e -> agregarEvento());
		inputPanel.add(agregarButton);

		JButton mostrarButton = new JButton("Mostrar Eventos");
		mostrarButton.addActionListener(e -> controlador.mostrarEventos());
		inputPanel.add(mostrarButton);

		JButton mostrarDiasButton = new JButton("Mostrar DÃ­as Faltantes");
		mostrarDiasButton.addActionListener(e -> controlador.mostrarDiasFaltantes());
		inputPanel.add(mostrarDiasButton);

		JButton eliminarButton = new JButton("Eliminar Evento");
		eliminarButton.addActionListener(e -> eliminarEvento());
		inputPanel.add(eliminarButton);

		JButton editarButton = new JButton("Editar Evento");
		editarButton.addActionListener(e -> editarEvento());
		inputPanel.add(editarButton);

		JButton completarButton = new JButton("Marcar Tarea Como Completada");
		completarButton.addActionListener(e -> marcarTareaComoCompletada());
		inputPanel.add(completarButton);

		return inputPanel;
	}

	public void setControlador(AgendaControlador controlador) {
		this.controlador = controlador;
	}

	public void mostrarEventos(List<Object> eventos) {
		textArea.setText("");
		for (Object obj : eventos) {
			if (obj instanceof EventoInterface) {
				EventoInterface evento = (EventoInterface) obj;
				textArea.append(evento.toString() + "\n");
			}
		}
	}

	public void mostrarDiasFaltantes(List<Object> eventos) {
		textArea.setText("");
		for (Object obj : eventos) {
			if (obj instanceof EventoInterface) {
				EventoInterface evento = (EventoInterface) obj;
				textArea.append(
						"Evento: " + evento.getNombre() + ", Dí­as faltantes: " + evento.getDiasFaltantes() + "\n");
			}
		}
	}

	public void mostrarMensaje(String mensaje) {
		JOptionPane.showMessageDialog(this, mensaje);
	}

	private void agregarEvento() {
		String nombre = nombreField.getText();
		String descripcion = descripcionField.getText();
		String fecha = fechaField.getText();
		String tipoEvento = tipoField.getText();
		String participantes = participantesField.getText();
		String lugar = lugarField.getText();
		String recordatorios = recordatoriosField.getText();
		controlador.agregarEvento(nombre, descripcion, fecha, tipoEvento, participantes, lugar,
				recordatorios);
	}

	private void eliminarEvento() {
		String nombreEvento = nombreField.getText();
		controlador.eliminarEventoDesdeVista(nombreEvento);
	}

	private void editarEvento() {
		String nombreEvento = nombreField.getText();
		String nuevoNombre = JOptionPane.showInputDialog("Nuevo nombre:");
		String nuevaDescripcion = JOptionPane.showInputDialog("Nueva descripción:");
		String nuevaFecha = JOptionPane.showInputDialog("Nueva fecha (yyyy-MM-dd):");
		controlador.editarEventoDesdeVista(nombreEvento, nuevoNombre, nuevaDescripcion, nuevaFecha);
	}

	private void marcarTareaComoCompletada() {
		String nombreEvento = nombreField.getText();
		controlador.marcarTareaComoCompletada(nombreEvento);
	}
}
package proyectofinalagenda;

import Model.AgendaModelo;
import Model.EventoFactory;
import Model.EventoInterface;
import Presentacion.AgendaVista;
import java.time.LocalDate;
import java.util.List;



public class AgendaControlador {
	private AgendaModelo agendaModelo;
	private AgendaVista vista;

	public AgendaControlador(AgendaModelo agendaModelo, AgendaVista vista) {
		this.agendaModelo = agendaModelo;
		this.vista = vista;
		this.vista.setControlador(this);
	}

	public void mostrarEventos() {
		List<Object> eventos = agendaModelo.obtenerEventos();
		vista.mostrarEventos(eventos);
	}

	public void mostrarDiasFaltantes() {
		List<Object> eventos = agendaModelo.obtenerEventos();
		vista.mostrarDiasFaltantes(eventos);
	}

	public void agregarEvento(String nombre, String descripcion, String fecha, String tipoEvento, String participantes,
			String lugar, String recordatorios) {
		try {
			validarEntradaEvento(nombre, descripcion, fecha, tipoEvento);
			EventoInterface evento = EventoFactory.crearEvento(tipoEvento, nombre, descripcion, fecha, participantes,
					lugar, recordatorios);
			agendaModelo.agregarEvento(evento);

			if (LocalDate.parse(fecha).equals(LocalDate.now())) {
				vista.mostrarMensaje("¡El evento se lleva a cabo hoy!");
			}

			vista.mostrarMensaje("Evento agregado exitosamente.");
		} catch (Exception e) {
			vista.mostrarMensaje("Error: " + e.getMessage());
		}
	}

    
}
package proyectofinalagenda;

import Model.AgendaModelo;
import Presentacion.AgendaVista;

/**
 *
 * @author NECSOFT
 */
public class ProyectoFinalAgenda {

    
    public static void main(String[] args) {
        new AgendaControlador(new AgendaModelo(), new AgendaVista());
        // TODO code application logic here
    }
    
}




## Authors

- [@konrad-daniel-sanchez](https://github.com/konrad-daniel-sanchez)


