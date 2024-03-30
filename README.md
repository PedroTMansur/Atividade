use std::collections::VecDeque;
use std::io::{self, Write};
use std::thread;
use std::time::Duration;

#[derive(Debug)]
struct Process {
    pid: usize,
    mem_size: usize,
    time_execution: u64,
}

impl Process {
    fn new(pid: usize, mem_size: usize, time_execution: u64) -> Self {
        Self {
            pid,
            mem_size,
            time_execution,
        }
    }
}

fn main() {
    let mut processes: VecDeque<Process> = VecDeque::new();

    loop {
        println!("Digite as informações do processo (memSize tempoDeExecução) ou 'sair' para encerrar:");
        let mut input = String::new();
        io::stdin()
            .read_line(&mut input)
            .expect("Falha ao ler a entrada");

        if input.trim() == "sair" {
            break;
        }

        let mut parts = input.trim().split_whitespace();
        let mem_size: usize = parts.next().unwrap().parse().unwrap();
        let time_execution: u64 = parts.next().unwrap().parse().unwrap();

        let pid = processes.len(); // Autoincremento para PID
        let process = Process::new(pid, mem_size, time_execution);
        processes.push_back(process);
    }

    println!("Resumo dos processos incluídos:");
    for process in &processes {
        println!("PID: {}", process.pid);
    }

    println!("Iniciando execução dos processos:");

    while let Some(process) = processes.pop_front() {
        println!("Iniciando execução do processo PID {}", process.pid);
        execute_process(&process);
        println!("Processo PID {} finalizado", process.pid);
    }

    println!("Resumo da execução:");
    println!("Consumo total de memória: {} bytes", total_memory_consumption(&processes));
    println!("Tempo total gasto na execução: {} segundos", total_execution_time(&processes));
}

fn execute_process(process: &Process) {
    let mut progress = 0;
    while progress <= process.time_execution {
        print!(
            "\rProcesso PID {}: [{}{}] {} segundos restantes",
            process.pid,
            "#".repeat(progress as usize),
            " ".repeat((process.time_execution - progress) as usize),
            process.time_execution - progress
        );
        io::stdout().flush().unwrap();
        thread::sleep(Duration::from_secs(1));
        progress += 1;
    }
    println!();
}

fn total_memory_consumption(processes: &VecDeque<Process>) -> usize {
    processes.iter().map(|p| p.mem_size).sum()
}

fn total_execution_time(processes: &VecDeque<Process>) -> u64 {
    processes.iter().map(|p| p.time_execution).sum()
}
